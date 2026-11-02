# Delivery plan

Status: task documents are split for focused execution; application capabilities remain unverified.

[Implementation plan](IMPLEMENTATION-PLAN.md) defines product scope, architecture proposals and release qualification. This index and its linked tasks define development delivery order. Each task links back here, to its milestone and to the implementation plan.

For one assignment, open its task file below, read the linked execution/safety rules, and follow only the references needed for that task. Shared contracts have one authoritative file.

## Shared references

- [Precedence and how to use this plan](delivery/execution.md)
- [Safety invariants for every packet](delivery/safety.md)
- [Provisional implementation choices](delivery/defaults.md)
- [Validation orchestrator contract](delivery/validation.md)
- [Choices needed at their point of need](delivery/decisions.md)
- [Requirement coverage](delivery/coverage.md)
- [Checking this plan](delivery/maintenance.md)

## Milestones and task lookup

| Milestone | Runnable result | Completion / order |
| --- | --- | --- |
| M0 | Fixed-click CLI on a private X11 fixture, real pixels/receipts and independent abort | First run [P0.6](delivery/tasks/P0.6.md); fault cases [P0.8](delivery/tasks/P0.8.md); attended fixed-run evidence [P0.9](delivery/tasks/P0.9.md) is separate |
| M1 | Existing image matcher clicks a moved target once; absence/duplicates never dispatch | [P1.1](delivery/tasks/P1.1.md)–[P1.4](delivery/tasks/P1.4.md) after [P0.6](delivery/tasks/P0.6.md); no production recognition claim |
| M2 | Electron record → review → edit → save → reload → run → stop | Shell/store [P2.1](delivery/tasks/P2.1.md)–[P2.2](delivery/tasks/P2.2.md) can follow [P0.6](delivery/tasks/P0.6.md) immediately; [P0.7](delivery/tasks/P0.7.md) observer before [P2.3](delivery/tasks/P2.3.md); automated loop [P2.7](delivery/tasks/P2.7.md), physical evidence [P2.8](delivery/tasks/P2.8.md) |
| Probes | Bounded Wayland and provider inventory | [PW.0](delivery/tasks/PW.0.md)–[PW.2](delivery/tasks/PW.2.md), [PP.1](delivery/tasks/PP.1.md)–[PP.3](delivery/tasks/PP.3.md) after [P0.6](delivery/tasks/P0.6.md); independent of X11 progress |
| M3 | Selected Chrome/Last Z scenarios, OCR, screen branches | [P3.0](delivery/tasks/P3.0.md)–[P3.5](delivery/tasks/P3.5.md), with each choice blocking only its dependent work |
| M4 | Complete recording, actions, model and editor | [P4.0](delivery/tasks/P4.0.md)–[P4.8](delivery/tasks/P4.8.md); recording extensions can start after M2 |
| M5 | Independent copy, partial re-record and undo | [P5.1](delivery/tasks/P5.1.md)–[P5.4](delivery/tasks/P5.4.md) |
| M6 | Outputs, debug/replay, recovery, acquisition | [P6.0](delivery/tasks/P6.0.md)–[P6.5](delivery/tasks/P6.5.md) after the relevant editor/actions; production evidence choices required here |
| M7 | Production guard/store/security and measured profiles | [P7.0](delivery/tasks/P7.0.md)–[P7.4](delivery/tasks/P7.4.md); no release claim until qualification |
| M8 | Wayland parity | [P8.0](delivery/tasks/P8.0.md)–[P8.4](delivery/tasks/P8.4.md), required passing backend cells |
| M9 | Independent provider integrations | [P9.0](delivery/tasks/P9.0.md)–[P9.4](delivery/tasks/P9.4.md); CLI failure does not block building Jev or shared Settings |
| M10 | Dual-session release and requirement closure | [P10.0](delivery/tasks/P10.0.md)–[P10.3](delivery/tasks/P10.3.md) after all applicable gates |

The **Depends on** fields are authoritative. The short path is:

### [M0](delivery/milestones/M0.md)

- [P0.0: Workspace and native toolchain](delivery/tasks/P0.0.md)
- [P0.1: Validation orchestrator](delivery/tasks/P0.1.md)
- [P0.2: Host line protocol and clock types](delivery/tasks/P0.2.md)
- [P0.3: X11 fixture target with receipt ledger and visual clock](delivery/tasks/P0.3.md)
- [P0.4: Capture helper and clock calibration](delivery/tasks/P0.4.md)
- [P0.5: Minimal guard: one request, current recipient and independent abort](delivery/tasks/P0.5.md)
- [P0.6: First runnable CLI: fixed replay with ordinary diagnostics](delivery/tasks/P0.6.md)
- [P0.7: Passive observation with in-process target filtering and the event–frame test](delivery/tasks/P0.7.md)
- [P0.8: Fault measurements](delivery/tasks/P0.8.md)
- [P0.9: Attended real-session check on X11](delivery/tasks/P0.9.md)

### [M1](delivery/milestones/M1.md)

- [P1.1: Existing matcher candidate and bounded dependency check](delivery/tasks/P1.1.md)
- [P1.2: Fixture scenes and automatic image-target draft](delivery/tasks/P1.2.md)
- [P1.3: Engine image locator: one dispatch or none](delivery/tasks/P1.3.md)
- [P1.4: Matcher report and fixture integration decision](delivery/tasks/P1.4.md)

### [M2](delivery/milestones/M2.md)

- [P2.1: Electron shell, IPC and helper supervision](delivery/tasks/P2.1.md)
- [P2.2: Workflow schema v0 and plain preview save/reload](delivery/tasks/P2.2.md)
- [P2.3: Recorder: ring frames, filtered events, extraction and target drafts](delivery/tasks/P2.3.md)
- [P2.4: Engine host: run, pause, resume, stop, explanation and run record](delivery/tasks/P2.4.md)
- [P2.5: Renderer: minimum journey pages](delivery/tasks/P2.5.md)
- [P2.6: Setup: stop key test](delivery/tasks/P2.6.md)
- [P2.7: End-to-end preview suite](delivery/tasks/P2.7.md)
- [P2.8: Attended real-session preview check](delivery/tasks/P2.8.md)

### [probes](delivery/milestones/probes.md)

- [PW.0: Wayland candidate proposal for D-PLATFORM-S](delivery/tasks/PW.0.md)
- [PW.1: Wayland interface inventory](delivery/tasks/PW.1.md)
- [PW.2: Attended Wayland pixels and dispatch probe](delivery/tasks/PW.2.md)
- [PP.1: Provider CLI inventory without account use](delivery/tasks/PP.1.md)
- [PP.2: Live CLI profile probe](delivery/tasks/PP.2.md)
- [PP.3: Jev fixture probe](delivery/tasks/PP.3.md)

### [M3](delivery/milestones/M3.md)

- [P3.0: Decisions: D-TARGETS, D-RECOG criteria, D-BRANCH, D-SEMANTICS values](delivery/tasks/P3.0.md)
- [P3.1: Web fixture and Chrome attach (X11)](delivery/tasks/P3.1.md)
- [P3.2: OCR read and find-text (Tesseract CLI)](delivery/tasks/P3.2.md)
- [P3.3: Check and branch core](delivery/tasks/P3.3.md)
- [P3.4: Last Z attended scenario](delivery/tasks/P3.4.md)
- [P3.5: Steam launch probe](delivery/tasks/P3.5.md)

### [M4](delivery/milestones/M4.md)

- [P4.0: Decisions: D-UI, D-SEMANTICS (sections, scopes, drafts)](delivery/tasks/P4.0.md)
- [P4.1: Gestures: double-click, drag, scroll, holds and chords](delivery/tasks/P4.1.md)
- [P4.2: Keys, layouts and authored text](delivery/tasks/P4.2.md)
- [P4.3: Loss, overflow and new-take hardening](delivery/tasks/P4.3.md)
- [P4.4: Region locator, safe click area and position-only sampler (R07, R13)](delivery/tasks/P4.4.md)
- [P4.5: Schema completeness](delivery/tasks/P4.5.md)
- [P4.6: Actions, Flow and Values editor (W10)](delivery/tasks/P4.6.md)
- [P4.7: Recording and running overlays](delivery/tasks/P4.7.md)
- [P4.8: Reference video and FFmpeg arrival measurement](delivery/tasks/P4.8.md)

### [M5](delivery/milestones/M5.md)

- [P5.1: Independent copy](delivery/tasks/P5.1.md)
- [P5.2: Candidate take for a selected range](delivery/tasks/P5.2.md)
- [P5.3: Mapping review, atomic replace and undo](delivery/tasks/P5.3.md)
- [P5.4: Board 09 UI and lock rows](delivery/tasks/P5.4.md)

### [M6](delivery/milestones/M6.md)

- [P6.0: Evidence comparison and decisions: D-SEMANTICS (outputs), D-RETAIN, D-PREDISPATCH, D-EVIDENCE-E, D-ACTIVE-RUN](delivery/tasks/P6.0.md)
- [P6.1: Values, text, screenshot and file outputs](delivery/tasks/P6.1.md)
- [P6.2: Debug mode and Runs replay](delivery/tasks/P6.2.md)
- [P6.3: Failure policies](delivery/tasks/P6.3.md)
- [P6.4: Acquisition and run preparation](delivery/tasks/P6.4.md)
- [P6.5: Evidence and storage failures](delivery/tasks/P6.5.md)

### [M7](delivery/milestones/M7.md)

- [P7.0: Decisions: D-NATIVE, D-STORE, D-RECOG, D-EXEC-E](delivery/tasks/P7.0.md)
- [P7.1: Guard hardening](delivery/tasks/P7.1.md)
- [P7.2: Store hardening](delivery/tasks/P7.2.md)
- [P7.3: Security and privacy hardening](delivery/tasks/P7.3.md)
- [P7.4: Integrated measurements for D-EXEC-Q and D-EVIDENCE-Q (X11)](delivery/tasks/P7.4.md)

### [M8](delivery/milestones/M8.md)

- [P8.0: Contract-writing packet from PW evidence](delivery/tasks/P8.0.md)
- [P8.1: Wayland capture adapter](delivery/tasks/P8.1.md)
- [P8.2: Wayland target-only observer](delivery/tasks/P8.2.md)
- [P8.3: Wayland dispatch, recipient and independent abort](delivery/tasks/P8.3.md)
- [P8.4: Wayland parity suite](delivery/tasks/P8.4.md)

### [M9](delivery/milestones/M9.md)

- [P9.0: D-AI and D-ROUTER](delivery/tasks/P9.0.md)
- [P9.1: Shared provider framework and Settings](delivery/tasks/P9.1.md)
- [P9.2: CLI adapters](delivery/tasks/P9.2.md)
- [P9.3: Jev runtime method](delivery/tasks/P9.3.md)
- [P9.4: OpenRouter (only if D-ROUTER selects it)](delivery/tasks/P9.4.md)

### [M10](delivery/milestones/M10.md)

- [P10.0: D-RELEASE, D-EXEC-Q, D-EVIDENCE-Q, D-SHORTCUTS](delivery/tasks/P10.0.md)
- [P10.1: Slice B](delivery/tasks/P10.1.md)
- [P10.2: Packages, offline journey and documentation](delivery/tasks/P10.2.md)
- [P10.3: Requirement closure](delivery/tasks/P10.3.md)

