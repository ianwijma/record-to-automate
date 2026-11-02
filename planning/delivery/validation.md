# Validation orchestrator contract

[Delivery index](../DELIVERY-PLAN.md) · [Implementation plan](../IMPLEMENTATION-PLAN.md)


[P0.1](tasks/P0.1.md) implements this contract, and later packets only add cases to it.

### 3.1 Command

```
bun run validate [selection] [options]
  selection:  --suite <name>[,<name>]   e.g. current, quick, m0, m1, m2, probe-wayland, release-x11
              --case <id|glob>[,...]    e.g. m0.guard.*, m1.image.moved
              --failed                  rerun fail/missing cases from artifacts/validate/last
  options:    --list                    print matching cases and their prerequisites; run nothing
              --keep-going              keep running after the first failure (default: stop the suite)
              --attended                allow manual cases to run their attended script ([shared §3](validation.md).4)
              --artifacts <dir>         default artifacts/validate
              --repeat <n>              run each selected case n times (flake and latency sampling)
```

`package.json` defines `"validate": "bun scripts/validate/index.ts"`. No selection runs `current`, the automated runnable milestone plus its prerequisite safety regressions. `quick` is an alias for `current`, never build-only. An explicit `unit` suite is available for local pure checks.

`validation/milestones.ts` declares required case IDs and `currentMilestone`; missing registered cases are `missing`, not omitted. [P0.1](tasks/P0.1.md) starts at `bootstrap` (summary says **no runnable program yet**, exit 2). [P0.6](tasks/P0.6.md) advances to M0 (launch fixture, capture, replay three clicks, verify receipts and abort/rejection cases); [P1.3](tasks/P1.3.md) advances to M1 (also moved/absent/duplicate); [P2.7](tasks/P2.7.md) advances to M2 (record → edit → save → restart → run → stop through Electron). Every later runnable integration packet adds its automated acceptance cases. Advancement changes selection before the first run, not only after tests pass; a broken milestone stays selected. Work not yet integrated is reported separately, never presented as complete.

Default validation always prints separate attended/qualification evidence status (`manual`, `missing`, `pass` or `fail`) and explicitly says **not qualified** where appropriate. Absent attendance does not block the automatic development loop or get counted as a pass. A safety failure for the current fingerprint/profile fails the overall run; historical results remain visible with their original profile, never silently reused as current evidence. Explicit milestone suites include their manual cases and return 2 when required evidence is missing. `--failed` reruns the preceding invocation’s failed/missing selected cases; after the fix, rerun the default to catch integration regressions. No empty selection or compile-only run may report a runnable milestone pass.

### 3.2 Exit codes and statuses

Each case returns `pass`, `fail`, `missing` (a prerequisite is absent) or `manual` (attended evidence is required and not present for this source/config/environment fingerprint). The process exit code considers selected cases, plus current-profile safety failures. Separate missing attended-status rows in default mode are informational ([shared §3](validation.md).1):

- `1` if any case returned `fail` or the artifact scan failed.
- Otherwise `2` if any case returned `missing` or `manual`, if the selection matched nothing, or if arguments were invalid.
- Otherwise `0`.

A case combining a hard safety assertion and a measurement is a `check` with metrics, not a measurement-only pass. A `measure` case is a case kind, not a status. Its `pass` means only that the measurement completed and was written. The summary lists measurement results separately and never counts them as capability passes.

### 3.3 Cases, prerequisites and artifacts

- A case is a module in `validation/cases/<suite>/<name>.ts` that exports `{ id, suite, kind: "check" | "measure" | "manual", requires: Prereq[], timeoutMs, run(ctx) }`. `scripts/validate/registry.ts` discovers the cases.
- The prerequisite probes live in `scripts/validate/prereqs.ts`: `bin:<name>`, `pkgconfig:<module>`, `xvfb`, `display:real-x11` (`XDG_SESSION_TYPE=x11` and a `DISPLAY` that is not Xvfb), `session:wayland`, `electron`, `authorization:<scope>` (existing session authorization or an executor-recorded reference to the user’s instruction), `decision:<id>` (resolved product choice) and `secret:<id>`. A missing prerequisite returns `missing` with a remediation line. Report the concrete missing prerequisite and whether it requires new authority. Reuse existing authority; request only genuinely new privileges/actions. A file’s existence never proves authorization, and a missing bookkeeping file never cancels authorization already given. The executor writes any needed record; the user does not create consent files.
- `ctx` provides `ctx.xvfb()` (starts a private `Xvfb :N -screen 0 1280x800x24 -nolisten tcp` and tears it down), `ctx.spawn()` (with log capture and process-group kill on timeout), `ctx.artifactDir`, `ctx.config` (`config/experimental.json`), `ctx.sentinels` (strings registered for the artifact scan) and `ctx.metric(name, value)`.
- Required native targets are built at the start of a selected run by `bun run build:native` if any source is newer than its binary. A compile failure is `fail`. Missing headers or `pkg-config` modules are `missing`.
- The orchestrator writes `artifacts/validate/<runId>/summary.json` with the commit, a content fingerprint of relevant source/config/lockfiles (including untracked source, excluding artifacts and archives), environment profile, the selection, statuses and metrics. It updates `artifacts/validate/last` as a symlink to that run. Each case directory holds `result.json` (`{id, status, kind, reason, durationMs, metrics, files[]}`), `stdout.log`, `stderr.log` and case-specific files. A failing case prints its rerun command: `bun run validate --case <id>`.
- `artifacts/` is listed in `.gitignore`.

### 3.4 Manual (attended) cases

- Without `--attended`, a manual case looks for `artifacts/manual/<caseId>/<fingerprint>.json`. If the file is missing, or its source/config fingerprint or session/backend profile differs, the case returns `manual`. If the file exists, the case returns the recorded result.
- With `--attended`, physical-desktop cases require their named real X11/Wayland session and refuse Xvfb; provider-only probes require no display. Each case prints the script from `validation/manual/<caseId>.md`, runs the automated measuring parts (fixture, helpers, ledger comparison), prompts the operator for the actions only a person can perform, and asks yes/no questions about what only a person can see. It then writes the evidence file. Ledger comparisons in that file are computed, never typed in.
- Synthetic input never stands in for physical recording or the physical abort chord. Physical-recording phases disable all synthetic drivers and require an empty guard dispatch ledger. Abort-during-replay phases explicitly permit the application replay, but the chord itself must come from the operator; record that distinction in the script and evidence.

