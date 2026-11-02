# Opus review of combined-v1

Reviewer: Claude Opus 5.5 high, 2 November 2026. Target: [combined-v1.md](../combined-v1.md), read with [MERGE-DECISIONS.md](../MERGE-DECISIONS.md), [DECISIONS.md](../../DECISIONS.md), [PLAN.md](../../PLAN.md), both plan-v2 and responses-v2 files. I did not read the other combined-v1 review. Nothing was implemented or tested live. The only primary documentation consulted is cited under OC-04.

## Verdict

**Ready after the listed changes.** There are no Blockers. The plan reads as one implementable plan. Its gates are honest, user-owned choices are labelled as such, and §12's "no limitation label as pass" rule holds across §6.3, W02 and W14.

The four High findings are contract fixes, not redesigns:

- The authorization rule ignores what the run's own earlier actions did to the screen.
- The M04 predicate-lifetime rule is not precise enough to put in a schema.
- The durable evidence-to-input latency, one of the four headline gates, has no early package that measures it.
- Inherited environment variables can silently move CLI use onto API billing.

**Counts:** Blocker 0, High 4, Medium 11, Low 2.

## Choices worth keeping

- **Freshness:** the deadline is the minimum over every observation still relied on, measured from capture time minus uncertainty. Durable assets and intent are written before the permit in every mode. The guard checks after the queue. A hash alone never counts as fresh confirmation.
- **Locators:** one selected locator per action, with no fallback to coordinates. The sampler validates the point after quantization.
- **Outputs:** effect identity is assigned before attempts. Uncertain reconciliation stops rather than writing a second copy.
- **Release:** guard SIGKILL is treated honestly, and any residual limitation needs user acceptance. An X11-only build is a development preview.
- **Providers:** Jev is required in the release and optional to use. OpenRouter is gated by D-ROUTER.
- **Editing and recovery:** copy and re-record confirm identity and meaning separately from type. Recovery counters survive retry and resume.

## Findings

### OC-01 High — Earlier actions do not invalidate evidence (§6.1–6.2; R07, R13, R14)

**Problem:** the deadline is purely time-based. If one frame locates both A and B, and clicking A opens an overlay over B, the click on B is still authorized within B's validity budget. Recorded click sequences usually run faster than any useful budget.

**Correction:** an observation may authorize a dispatch only if its earliest capture time comes after the previous side effect in the same lease was acknowledged and its settle time passed. Two exceptions: segments of a single action (drag, hold, chord, text) and steps the author marks non-interfering with a traced reason. Carry this prior-dispatch boundary in the permit, and have the guard reject permits that violate it.

**Validation:** fixture plus FakeGuard: an overlay-creating A and a covered B located in the same frame. Expect no dispatch to B until a frame captured after A exists.

### OC-02 High — The M04 lifetime rule is not yet a schema rule (§6.1; R06, R11)

**Problem:** "each subsequent action declares…" and "immediate action" are undefined. The plan does not say:
- what the default is when nothing is declared;
- what happens when a wait or extraction step comes before the first click;
- how else arms work, where the selecting predicate is an absence.

**Correction:** define a decision scope. The selecting predicates, including absence predicates, are active preconditions of every step in the arm up to and including its first side effect. That dispatch ends the scope, per OC-01. After that, steps rely on their own locators and checks, and authors add explicit re-check steps rather than inheriting a lapsed predicate. An expired predicate triggers a complete rerun of the judgment, and each rerun counts against the step's readiness timeout, so the step cannot livelock. State the residual observe-to-dispatch race as a measured limitation that post-action verification detects.

**Validation:**
- Popup-dismiss: the popup predicate is consumed by the dismiss click, and a later menu click does not require it.
- Changed screen: a "Shop" judgment, then the screen changes to one with an identical Buy icon before dispatch. Expect a rerun or a rejection.
- Else arm: a popup appears after the "absent" decision. Expect a rejection or a failed verification.
- Slow Jev on an animated screen: the step reaches its policy before the timeout expires.

### OC-03 High — The durable critical path has no early gate (§6.2, §10, §12)

**Problem:** §12 names freshness with durable evidence as a top gate, and §6.2 requires measuring it before choosing limits. But W01 covers pixels, dispatch and abort, W03 covers recognition, and W04 covers Jev. No package measures capture → locate → asset and directory fsync → intent → queue → guard → dispatch until W09, after W05–W08 are built.

**Correction:** add a G-L cell to W01 (and to W02 when available) using provisional store code. Measure latency percentiles in basic and debug modes, under encode load, on the candidate filesystem, against the fixture's change rates. If the results are infeasible, return evidence-placement options to the user under D-EVIDENCE and D-EXEC before W05/W09.

**Validation:** a G-L report with percentiles, sample counts and permit-expiry rates.

### OC-04 High — Inherited environment can switch CLI use to API billing (§9; R10)

**Problem:** Claude Code documents that when `ANTHROPIC_API_KEY` is set, it "is used instead of your … subscription even if you are logged in. In non-interactive mode (`-p`), the key is always used when present" ([env vars](https://code.claude.com/docs/en/env-vars)). Codex documents modes where "the process environment controls authentication" ([auth](https://learn.chatgpt.com/docs/auth?surface=cli)). §9 profiles cover flags and configuration but not the spawn environment.

**Correction:** spawn from an allowlisted environment. Detect and strip, or disclose, credential and provider-routing variables and settings: API keys and tokens, Bedrock/Vertex selectors, `apiKeyHelper`, and the Codex key/token variables, using the list documented for the probed version. Before enabling an adapter, verify the active authentication source from the CLI's own status output and show it under the Settings billing category.

**Validation:** a sentinel case for each variable. Expect the CLI to report subscription authentication or the adapter to be refused.

### OC-05 Medium — D-EXEC and D-EVIDENCE gates are circular (§2; W06/W07/W09/W13)

**Problem:** these packages require approved measured limits, yet those limits come from the production path the same packages build.

**Correction:** split the gate. D-EXEC-1 is an experimental envelope from W01, W03 and G-L data; it gates implementation and Slice A. D-EXEC-2 and the final D-EVIDENCE defaults gate qualification at Slice B. W09 also lists D-EVIDENCE for the minimum basic-mode evidence.

**Validation:** every D-gate's "blocks" entry names work that comes after the gate's own inputs exist.

### OC-06 Medium — Pinned CLI versions have no mismatch policy (§9; R10)

**Problem:** frequent CLI updates leave the adapter either usually disabled or running isolation that was never tested on that version.

**Correction:** set a version floor. On a version change, run a user-started re-qualification on the user's machine: sentinel hooks, plugins and MCP, a settings hash, and the authentication source. Keep the adapter disabled with an explanation until it passes, and disclose any account usage the probe consumes.

**Validation:** version-bump fixtures covering pass, fail and cancel.

### OC-07 Medium — The guard's recipient check has no source (§3, §6.2; R01)

**Problem:** the guard owns no capture, so a recipient check after dequeue either compares the permit with itself or reintroduces a slow-service dependency.

**Correction:** specify the source per backend. On X11, query focus and the window under the pointer on the guard's own connection. On Wayland, use whatever W02 proves. Otherwise, include a recipient observation inside the permit deadline and state the remaining check-to-dispatch window.

**Validation:** inject focus theft before dequeue and before dispatch, on each backend.

### OC-08 Medium — Pause semantics are missing (§6.3; R14)

**Problem:** pausing for help implies the user physically operates the app, but the plan never says what pause does to permits, held inputs, the lease or recording.

**Correction:** pause revokes queued permits, releases guard-owned inputs, and suspends the lease with no recording. Resume starts a new epoch and re-checks readiness. Evidence captured before the pause authorizes nothing afterwards.

**Validation:** pause mid-drag, have the user interact, then resume. Expect no stale dispatch and no recorded user events.

### OC-09 Medium — Target-only filtering is stated only for the broker (§4; R04)

**Problem:** X11 RECORD delivers every application's events to the capture service, but the discard-before-persistence rule appears only in the broker paragraph.

**Correction:** make target-only filtering a recorder contract on every backend. It covers persisted events, renderer IPC, logs, crash dumps and diagnostics.

**Validation:** non-target key sequences are absent from all of those outputs.

### OC-10 Medium — Required cells and Wayland client coverage are undefined (§4; W01/W02/W06/W14)

**Problem:** "W01 passed" names no required cells, so IME text insertion could either block W06 or quietly become optional. It is also unstated whether Wayland support requires native Wayland clients, including native-Wayland Chrome, or whether XWayland clients suffice.

**Correction:** add a cell classification for D-PLATFORM approval:
- **Required:** pixels, pointer observation, physical-key observation with event-time recipient, dispatch, abort, release.
- **Reported:** text methods, IME, Compose.

Also add an explicit Wayland client-coverage choice to the support claim.

**Validation:** the W18 matrix lists each cell's class and outcome.

### OC-11 Medium — Jev (W16) depends on CLI work (W15) (§10; R11)

**Problem:** Jev is a hosted HTTP method, yet a failed W04 CLI profile blocks W15 and therefore R11. W17 has the same coupling.

**Correction:** extract a shared provider/Settings package for capabilities, typed errors, cancellation, data selection and secrets, depending on W09/W10 and D-AI. W15, W16 and W17 then depend on it independently.

**Validation:** W16 remains reachable when W04's CLI cells fail.

### OC-12 Medium — Recognition adequacy has no owner or failure route (§7; W03→W07)

**Problem:** nothing defines who approves per-class adequacy, or what happens if Last Z text or icons fail. Jev is correctly excluded as a substitute.

**Correction:** add D-RECOG. Its options on failure are another local OCR engine, authored region or fixed targets for that class, or a user scope decision.

**Validation:** the G-R report records an adequacy decision per class.

### OC-13 Medium — The default target for recorded clicks is unspecified (§4, §7; R04, R16)

**Problem:** the authors differed (review required vs an automatic image target), and the combined plan picks neither. The answer determines whether a 30-click recording needs 30 reviews before it can run.

**Correction:** recommend an automatic image target cropped from the lossless pre-frame, runnable immediately. A batch review flags only targets that are non-unique or low-contrast in their own recording frame. Variation stays off until the user confirms a safe area.

**Validation:** record then run on the fixture with no edits; flagged targets block the run.

### OC-14 Medium — Too many spike approvals, and no named approver (§2)

**Problem:** agreeing allowance, budget, matrix, sample size and pass criteria per capability means roughly 15–25 separate agreements, and the plan does not say who agrees.

**Correction:** the user approves one W00 spike protocol. The implementer logs each cell's parameters before testing and escalates only deviations, failures and privileged setup.

**Validation:** W00 delivers the protocol and the parameter log.

### OC-15 Medium — The release matrix lacks safety-invariant rows (§11; W18)

**Problem:** abort and release, no unchecked dispatch, and the `OutcomeUnknown` stop are the plan's core guarantees, yet none has an R-row. W18 could therefore close without Wayland evidence for them.

**Correction:** add two rows:
- **R19:** abort and release on each backend, including the stall matrix and SIGKILL outcome.
- **R20:** permit, freshness and single-use invariants, plus `OutcomeUnknown` reconciliation.

**Validation:** W18 evidence is linked for both sessions.

### OC-16 Low — Output staging leftovers and attempt hashes (§8; R08)

**Problem:** staging files can be left behind in the user's destination folder, and retried screenshots change content hashes.

**Correction:** name staging files by logical identity and clean up only names known to the journal, under containment checks. During reconciliation, accept the hash of any journaled attempt. Retry the same identity only when the earlier attempt is proven unpublished.

**Validation:** kill at each publication boundary, then rerun. Expect one output and no leftover staging files.

### OC-17 Low — Cleanups

- Assign the invocation-shortcut work for R03, especially on Wayland, to a W package.
- Rename "offline basic mode": offline and non-debug are separate ideas.
- Drop "relevant D-UI" from W05, and let schema and store work start before the protocol's W01–W03 inputs arrive.
- Define what happens when no secure store is available: either a session-only key or a refusal to persist.

## Highest-priority changes

1. Add evidence invalidation by earlier actions and the decision-scope rule, with its counterexample tests (OC-01, OC-02).
2. Add the early G-L gate and remove the circular D-EXEC/D-EVIDENCE placement (OC-03, OC-05).
3. Add CLI environment and authentication-source isolation, plus re-qualification on version change (OC-04, OC-06).
4. Add the required-cell and Wayland client-coverage classification, and the safety rows R19/R20 (OC-10, OC-15).
