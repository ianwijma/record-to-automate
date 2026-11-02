# Opus review of Astra plan v1

Reviewer: Claude Opus 5.5 high. Target: `planning/implementation/astra/plan-v1.md`. Baseline: DECISIONS.md, PLAN.md, the approved visual study. My own plan is context only. No live tests were run. External claims are linked next to the claim.

## 1. Verdict

**Ready to synthesize after fixes.** The plan respects every confirmed decision I checked, and its safety contracts are the stronger of the two drafts. The four High findings are missing contracts plus one incorrect platform assumption. None needs a redesign.

**Retain in synthesis:**

- the single-use `DispatchPermit`, bound to observation, epoch, deadline and geometry hash, re-checked by the guard and invalidated on reconnect;
- `Found | Absent | Ambiguous | Unavailable`, with method-specific scores;
- honest key capture: physical keys do not reveal IME text, so text is an explicit action;
- create-new-only outputs with no-replace publish and reconciliation by identity (safer than my four modes);
- the delayed-judgment rule for Jev;
- "if isolation cannot preserve eligible login, D8 reopens" (stricter than my plan);
- investigation stop criteria that never silently switch compositor or drop Wayland;
- intent written before dispatch, `OutcomeUnknown` with no automatic retry, and failing closed when the disk is full.

**Requirement coverage** is in the release dependency closure (A07 depends on A04–A06): per-action debug frames and the actual run path (A05), replay that dispatches nothing (A05), independent copies, position-only variation, screen conditions without subworkflows (D5), offline acceptance with networking blocked, CLI and Jev settings in the UI (A06), and OpenRouter deferred under the user-owned D8. The exception is launch/attach (OA-11).

## 2. Findings

### OA-01 High: the privileged input broker has no security contract (missing contract; §2 broker, D4, A02)

**Problem.** The Wayland recorder reads keyboard and pointer evdev devices through a broker that "authenticates the active local session" without `input` group membership. The plan never specifies the privilege mechanism, what is streamed, or whether it filters by focus.

**Consequence.** A public release would ship a privileged keyboard reader. During recording it would capture keystrokes typed into other applications, including passwords. D4 and D7 packaging cannot be judged without these details.

**Correction.** Specify:

- the mechanism, for example a systemd service with polkit `allow_active` for the seat's active session, plus an IPC peer check;
- streaming only during a recording lease;
- dropping events not aimed at the target, using the extension's focus identity, before they reach persistence;
- a visible indicator, no device grabs, and no command surface.

**Acceptance evidence.** A threat-model note. Keys typed into a non-target window never reach `events.jsonl`. The broker refuses inactive or remote sessions. Uninstalling removes the service.

### OA-02 High: the event-time Wayland coordinate gate has no candidate mechanism (feasibility and sequencing; §2 "hardest cell", A00)

**What the plan gets right.** It rejects evdev deltas, `get_pointer` polling, per-frame cursor samples and stage events. That is correct: Mutter can deliver events to Wayland clients without stage emission.

**Problem.** No mechanism is named that could pass. Two harness days plus two repair days per backend must cover an extension, a broker, ScreenCast, RemoteDesktop and association testing. A false "unsupported" is the likely outcome, which would send D4 back to the user without usable evidence.

**Correction.**

1. Name the candidates:
   - (a) an extension or Mutter hook that tags each button event with stage coordinates and the target surface at delivery;
   - (b) XWayland-scoped RECORD/XI2 for XWayland clients (Proton's default), reported as partial coverage only;
   - (c) ScreenCast cursor metadata, as a cross-check only.
2. Budget per capability cell, with a separate build allowance for the broker and extension.
3. Set numeric pass criteria from the fixture ledger: zero misassociated clicks across move-click-move scripts at several pointer speeds, position error within an approved tolerance after scaling, and event loss counted.

**Stop rule.** If neither (a) nor a deeper compositor bridge passes, D4 reopens with the evidence.

### OA-03 High: GNOME 46 probably lacks the GlobalShortcuts portal, and no evdev abort route exists during runs (technical correctness; D4, §2 Shortcuts/Abort, §3)

**Problem.** GNOME's GlobalShortcuts backend reportedly landed in GNOME 48 (xdg-desktop-portal-gnome MR 208; secondary source [r/gnome, Feb 2025](https://reddit.birdcat.cafe/r/gnome/comments/1iyr5t1/global_shortcuts_portal_has_landed_in_gnome_48); G-W0 must confirm). On Ubuntu 24.04 / GNOME 46 that candidate is then unavailable. The broker "closes on stop", so it gives no abort chord during runs. §3 makes an out-of-focus abort a release requirement, yet the proposed target may have no working route.

**Correction.** Name a route per compositor:

- an extension keybinding that signals the guard over a dedicated channel; or
- the broker in a run mode that matches only the reserved abort chord; or
- re-evaluate a GNOME ≥48 LTS in D4. My plan's Ubuntu 26.04 suggestion is also unverified.

**Acceptance evidence.** Abort works with the fixture and game focused and fullscreen, and with the engine stalled. Behaviour with the extension disabled is reported as a failure mode.

### OA-04 High: durable writes and dispatch freshness have no ordering (missing contract; §5, §6, D6)

**Problem.** §6 persists intent and pre-frame before authorizing input. D6 budgets 150 ms for observation age plus uncertainty, with a recheck against the newest frame. A lossless PNG write, a journal fsync and an OCR confirm can each use up that budget. Because the plan never says whether the deadline is checked before or after the write, either:

- permits expire until timeout; or
- the check happens early and a stale point is dispatched.

It is also not stated that non-debug runs write intent records. Without them, a crash cannot produce `OutcomeUnknown`.

**Correction.**

- Write frame assets during locate.
- Fsync only a small intent record on the critical path.
- The guard checks `deadlineNs` after that durable write; an overrun re-observes.
- Confirm targets by comparing the located region's pixels, not by running OCR again.
- Write intent records in every mode.

**Acceptance evidence.** Distributions of fsync time and observation-to-dispatch age. A slow-fsync injection causes re-observation, never a stale dispatch.

### OA-05 Medium: held inputs after guard SIGKILL (§3 lifecycle, A03)

The watchdog covers engine death, and the plan honestly labels guard failure a limitation. Nothing releases held inputs when the guard itself is killed mid-drag. XTest or RemoteDesktop release on disconnect is not established.

**Correction.** The guard reports held-state changes to a supervisor before each press. On guard EOF, the supervisor releases through a fresh connection.

**Evidence.** Per backend, SIGKILL during a drag and during a key hold, then check the ledger. Any residual failure is documented.

### OA-06 Medium: no replay contract for keys and text (§4, §2 dispatch row)

Unspecified: keycode versus keysym dispatch, behaviour when the replay layout differs, and Unicode text entry per backend. XTest needs keymap remapping. RemoteDesktop has both keycode and keysym methods.

**Correction.** Store evdev code, keysym, layout ID and mode (physical or text). A layout mismatch blocks or warns. Add a per-backend text-entry gate and a masking capability; the redaction policy stays user-owned under D6.

**Evidence.** Non-US layout, AltGr and non-ASCII text cells.

### OA-07 Medium: fallback rules between targets are undefined (§5)

The default target for a recorded action is unspecified, and so is whether a visual target may fall back to its fixed coordinate. Falling back after `Absent` or `Ambiguous` is the unchecked click PLAN.md forbids.

**Correction.** Never fall back automatically on `Absent` or `Ambiguous`; `Unavailable` is an error. Allow only explicit user chains in which every element carries its own check. The trace records which locator authorized the dispatch.

**Evidence.** An absent image target with a stored fixed point sends zero input.

### OA-08 Medium: re-record identity and output types (§4 replacement, A04)

**Correction.**

- Confirmed mapped steps keep their step, target and output IDs; new steps get new IDs.
- `ValueRef.type` is revalidated at commit, and a type change lists consumers and blocks running.
- Add "bring the app to this starting screen" guidance and a similarity check before candidate recording.

### OA-09 Medium: recovery semantics (§3, AST in §4, D5)

Recovery blocks exist but are missing from the AST and have no exit semantics. A recovery that retries its step is a back-edge, and loops remain an open user decision.

**Correction.** Make recovery a step policy with exit `retry-step | continue | stop`, bounded per step and per run, never nested, and drawn in Flow as an annotation rather than an edge.

**Evidence.** Validator tests, plus a test that an exhausted bound stops with an explanation.

### OA-10 Medium: name the documented CLI isolation flags, and fix the image-input conflict (§6, A06)

**Mechanisms now documented.**

- **Claude:** `--safe-mode` disables CLAUDE.md, plugins, hooks and MCP while "Authentication, model selection, built-in tools, and permissions work normally". Managed-policy hooks still apply. `--restricted` (v2.1.248+) loads only managed settings plus `--settings` and confines the file tools. Also `--tools` and `--strict-mcp-config`. Source: [CLI reference](https://code.claude.com/docs/en/cli-reference).
- **Codex:** `exec --ignore-user-config` ("Authentication still uses `CODEX_HOME`") and `--ignore-rules`. No `exec` flag that disables hooks or MCP was confirmed. Source: [Codex commands](https://learn.chatgpt.com/docs/developer-commands?surface=cli).

**Conflict.** "Deny desktop tools" may break Claude image input. Images then need stream-json image content or a Read tool confined to the scratch directory.

**Correction.** Record version floors, disclose that managed policy cannot be overridden, and run the smoke test under the exact flags. The D8-reopens rule stays.

### OA-11 Medium: sequencing gaps (§7)

- **A00 waits on the wrong approvals.** It depends on D1–D4, including React and C++, but those spikes should inform D2/D4. Run A00 on provisional spike code first.
- **Launch and attach have no work package.** Window selection, Steam launch and staged readiness appear only in A07 acceptance. Add them to A03, as PLAN.md Phase 1 does.
- **Recognition has no gate.** There is no stop rule or alternative if Tesseract fails on Last Z (for example ONNX OCR, or Jev as an explicit online option). Add a recognition gate with a wrong-target metric before A04.

### OA-12 Medium: crash safety and output retry (§4)

**Gaps.**

- No directory fsync after create or rename, for revisions, assets, runs, the manifest or the output folder.
- No cleanup of orphaned staging files.
- No fallback on filesystems without no-replace rename or hard links (vfat/exFAT, some FUSE/NFS).
- A recovery retry must reuse the same identity and reconcile, not write a second file.
- A live `OutcomeUnknown`, such as a guard pipe break, must also stop with no retry.

**Evidence.** Kill tests at each fsync boundary, and a vfat output test.

### OA-13 Low: crop source (§4)

Require a lossless ring buffer during recording, so template crops never come from VP8 video.

### OA-14 Low: permission prompts and setup (§2)

Measure portal prompts per run and whether restore tokens work, for ScreenCast and RemoteDesktop. Specify the in-app setup and diagnostics flow for the extension and broker, since ease of use is the top priority.

### OA-15 Low: semantic Jev judgments (§6)

Branch-selecting judgments follow the delayed-judgment rule. An uncertain judgment means `unknown`, not `false`. Measure the rule on animated regions before approving the capability.

## 3. Tradeoffs against my plan

- **C++ (Astra) versus Rust (mine).** This is a preference, not a defect. Astra's direct Tesseract and OpenCV C++ use is a real advantage. My claim that Rust libei/PipeWire coverage is mature is unproven. Decide D2 after the X11 spike.
- **Astra is better on:** key capture and IME (my "printable keys → type text" is wrong), output safety, permits, Jev staleness, CLI isolation strictness, Electron security, and stop criteria.
- **Mine should contribute:** lossless pre-frames, XI2 `sourceid` separation, an XWayland RECORD candidate, physical input as a secondary abort signal, mapped re-record steps keeping their IDs, recovery exit semantics, `unshare -n` offline CI, a recognition corpus with a zero-wrong-target rule, restore-token evidence, and a doctor command.
- **Mine needs revision on:** the "image target with fixed fallback" default (violates OA-07), the overwrite and append modes (no retry or `OutcomeUnknown` semantics), and the compositor candidate (needs G-W0 confirmation).

## 4. Recommendations

- **To the author:** fix OA-01 to OA-04 in the text, fold OA-05 to OA-12 into the contracts and work packages, and keep the Low findings as acceptance items.
- **For synthesis, adopt:** Astra's permit, LocateResult and output models, its investigation discipline, the named CLI flags behind capability detection, a per-compositor abort route, and the broker security contract.
- **Still the user's to decide:** D2 helper language, D4 compositor and broker/extension acceptance, D5 outputs and section splitting, D6 numbers, retention and write-failure policy, D7 license and packaging, D8 OpenRouter priority.

---

Artifact: `/home/ian/record-to-automate/planning/implementation/reviews/opus-on-astra-v1.md`

Findings: Blocker 0, High 4, Medium 8, Low 3.

Three highest-priority fixes:

1. OA-02 and OA-01: candidate mechanisms and numeric pass criteria for event-time Wayland coordinates, plus a security and focus-filtering contract for the privileged broker.
2. OA-03: a working out-of-focus abort route on GNOME 46, where GlobalShortcuts is likely absent and the broker is closed during runs.
3. OA-04: order durable intent writes against the freshness deadline, with intent records in every mode.
