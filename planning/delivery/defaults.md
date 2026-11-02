# Provisional implementation choices

[Delivery index](../DELIVERY-PLAN.md) · [Implementation plan](../IMPLEMENTATION-PLAN.md)


These are implementer proposals for disposable fixture previews, not user-approved product defaults. They may be adjusted with a short reason and rerun evidence within the authorized scope. Do not freeze them into release semantics.

| Area | Early implementation | Later gate |
| --- | --- | --- |
| Workspace | Bun workspaces, strict TypeScript, `bun test` | Pin actual dependencies during implementation |
| Native X11 | One small C11 host executable, with separate guard and capture worker processes; add an observe worker mode only for [P0.7](tasks/P0.7.md). Xlib/XTEST first; RECORD and related headers only when recording needs them | D-NATIVE in [P7.0](tasks/P7.0.md); compare the working candidate with C++/Rust only as needed for production |
| Renderer | React + Vite + Tailwind; copy named design tokens from `design/journey.html`, not line-number ranges | D-UI before production renderer |
| Shell | Electron sandbox/context isolation, narrow preload, engine utility process | Existing architecture proposal |
| Matcher | Existing OpenCV candidate through the simplest available local binding/subprocess; bounded dependency feasibility in [P1.1](tasks/P1.1.md) | D-RECOG for production. No bespoke CV implementation |
| OCR | Existing Tesseract CLI on a crop | D-RECOG at [P3.2](tasks/P3.2.md) |
| Preview store | One replaceable workflow JSON and ordinary referenced image files per project; temp-file rename for saves; copy the exact document into each run | D-STORE; immutable manifests/content-addressing/migrations in [P7.2](tasks/P7.2.md) |
| Preview evidence | In-memory bounded trace plus ordinary diagnostic writes. No per-input fsync or durable evidence prerequisite; write errors stop, missing evidence is explicit, restart never resumes a run | D-PREDISPATCH and D-RETAIN before production evidence behavior |
| Recording emergency | Stop and mark the current in-memory take incomplete; disable saving it until D-EMERGENCY-TAKE is decided. Do not silently persist or delete a saved take | D-EMERGENCY-TAKE at [P2.3](tasks/P2.3.md), only when this behavior is needed |
| UI test driver | `playwright-core` Electron driver on private Xvfb | Verify dependency availability in [P2.1](tasks/P2.1.md) |
| Abort chord | Provisional `Ctrl+Alt+Shift+F12`, including Lock/NumLock variants | D-SHORTCUTS and D-EXEC-Q |

`config/experimental.json` labels all numbers experimental. Start with `watchdogMs: 1000`, `permitValidityMs: 500`, `settleMs: 300`, `ackTimeoutMs: 1000`, `recordFps: 15`, `ringFrames: 45`, `observeBufferEvents: 1024`. Capture lag has **no assumed safe default**: [P0.4](tasks/P0.4.md) measures a fixture-only bound and writes it with the measured environment and margin. A missing/stale bound blocks dispatch. Matcher thresholds are named and recorded by [P1.1](tasks/P1.1.md) for the actual candidate and fixtures; they are not universal accuracy claims. Bounds changed after a failure require rerunning affected cases, never hiding the failure by relabelling it.

Preview artifacts are local, test-run-scoped diagnostics, not a product retention policy. Record their location and explicit cleanup command; do not install automatic deletion or claim crash/power-loss durability. Real-target screenshot retention must be resolved before those captures, without blocking fixture work.

