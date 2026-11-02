# Astra implementation plan v1

Research date: 2 November 2026. Independent proposal; no implementation or live platform/provider tests performed. Read the complete DECISIONS.md, PLAN.md and README.md, plus relevant interaction code in the approved visual study. No applicable AGENTS.md found. This document does not approve its recommendations.

## 1. Status and implementation decisions

**Confirmed:** easy-to-use, open-source Linux desktop automation; both X11 and Wayland; current Pop!_OS 22.04/GNOME 42.9/X11 first; Last Z through Steam/Proton, then other applications including Chrome. Automatic video/action recording, editable targets, independent copies, partial replacement/undo, screen branches, extracted values/file outputs, offline core, optional own unmodified Claude Code/Codex CLIs and Jev, optional OpenRouter. Preserve spacious dark Actions/Flow/Values and add Runs. Variation affects safe click position only. Missing/ambiguous/stale targets never authorize unchecked input; timeout defaults to stop-and-explain. Source recording, edited intent and execution evidence remain distinct.

**Proposed decisions, requiring user approval before dependent implementation:**

| Gate | Recommendation and tradeoff |
| --- | --- |
| D1: UI/runtime | React + TypeScript + Tailwind, Vite, Electron; Bun for workspace/build tooling, Electron's Node runtime for services. React fits the linked inspector views; Svelte is a smaller alternative but offers insufficient benefit to add a second evaluation track. Pin exact versions after packaging proof. |
| D2: native boundary | One C++20/CMake helper family using Xlib/XRecord/XTest, GStreamer, Tesseract and OpenCV; separate process modes for capture, recognition and input guarding. Direct native libraries avoid Electron ABI bindings; C++ adds memory-safety maintenance compared with Rust. Use RAII, sanitizers and constrained IPC. |
| D3: storage | Directory project: immutable JSON revisions, content-addressed assets, append-only run journals; rebuildable JSON catalog. Prefer inspectability and recovery over SQLite's queries; reconsider SQLite only if measured history size defeats indexed browsing. |
| D4: Wayland | First investigate Ubuntu 24.04/GNOME 46/Mutter, pinning actual patch/backend versions in evidence. Recommend a narrowly scoped GNOME extension plus explicitly authorized input-device broker if required for passive recording. Setup burden and compositor maintenance require approval; this is not demonstrated support. |
| D5: semantics | Structured sequences/if-else, run-local typed values and explicit joins; create-new file outputs first. Defer globals, loops, subworkflow calls and schedules. Approve section-splitting interaction and output rules before implementation. |
| D6: defaults/evidence | Approve measured timeout, freshness, recognition, shortcut, recording and retention profiles before shipping. Candidate values below are experiments, not accepted defaults. |
| D7: release | Apache-2.0 for original code; Debian packages for the two proposed Linux environments, manual updates initially. Bundled native dependencies/models need a distribution inventory. AppImage later; Flatpak needs separate broker/extension integration proof. |
| D8: providers | Ship optional CLI assistance and experimental Jev; recommend OpenRouter after initial Linux release. Approve priority and demonstrated account/model capabilities, without a universal subscription promise. |

## 2. First evidence gates

Implement only after planning approval. Build a harmless fixture application first: event ledger, moving/repeated targets, counter text, delayed readiness, draggable object, keyboard field, popups and controllable animation. Run it as native Wayland and XWayland, plus X11. Its received-event ledger is ground truth; screenshots alone cannot prove input delivery.

**Investigation budget:** per backend, one two-engineer-day harness pass, then one two-day targeted repair pass. Each capability below gets its own reproducible transcript, video, event ledger and exact environment manifest. A failed/unfinished cell remains unsupported. After the repair pass, return evidence and a changed architecture/setup proposal to the user; do not extend indefinitely, switch compositors silently, substitute an interactive-preview UX, or remove Wayland from release acceptance.

| Capability | X11 candidate | Wayland candidate | Required evidence |
| --- | --- | --- | --- |
| Pixels | GStreamer X11 capture | ScreenCast/PipeWire | Correct target frames during resize, occlusion, fullscreen and source loss; permission denial is explicit. |
| Passive observation | XRecord device events | Non-grabbing evdev observer, GNOME coordinate/focus bridge | Ordinary physical clicks/keys/drags still reach fixture; both edges of sub-frame clicks retained; no event loss hidden by interpolation. |
| Dispatch | XTest | RemoteDesktop authorized pointer/keyboard methods; EIS where supported | Fixture receives requested order/coordinates; native Wayland and XWayland tested separately. |
| Coordinates | Root/client/frame mapping | Stream/compositor/input-region mapping | Corners/interior hit correctly after movement, mixed scaling, monitor rotation/hotplug; unknown mapping blocks execution. |
| Shortcuts | X11 key grab | GlobalShortcuts portal, extension fallback | Start/pause/abort observed while target is focused; conflicts/denial detected. |
| Abort | Dedicated input guard | Same guard plus independently tested shortcut route | Abort under UI/recognizer/provider stall; held input released on engine death; injected dispatch stops. |

XRecord and XTest specifications describe observation and synthesis separately; the baseline's advertised extensions prove availability only. [XRecord](https://www.x.org/archive/X11R7.5/doc/Xext/record.pdf), [XTest](https://www.x.org/releases/current/doc/xextproto/xtest.pdf).

ScreenCast supplies streams, RemoteDesktop authorized control and GlobalShortcuts shortcut activation. Their existence does not establish installed backend support. InputCapture sends activated input to its client instead of updating the normal pointer: it is not the passive recorder required here. [ScreenCast](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html), [RemoteDesktop](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.RemoteDesktop.html), [GlobalShortcuts](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.GlobalShortcuts.html), [InputCapture](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.InputCapture.html).

**Wayland's hardest cell:** evdev reports device changes, not authoritative accelerated screen positions. Investigate timestamped compositor coordinates/focus through the extension, associated with physical events. GNOME documents pointer-coordinate access, but current documentation is not proof of event-time accuracy or GNOME 46 compatibility. Do not integrate relative deltas as absolute positions or assume shell stage events observe application input. Rapid move-click-move tests must disprove misassociation. If event-time positions cannot be established, require a deeper compositor bridge or reconsider the first compositor through D4. A screenshot cursor sampled once per frame is insufficient. [Kernel events](https://www.kernel.org/doc/html/latest/input/event-codes.html), [GNOME pointer API](https://gnome.pages.gitlab.gnome.org/gnome-shell/shell/method.Global.get_pointer.html).

Broker proposal: authenticate the active local session, authorize selected device reads, never grab devices, close on stop/logout, grant no general command execution. Do not require permanent membership in the input group. Losing permissions, event synchronization or extension connectivity stops recording/run eligibility. This complexity is the principal D4 tradeoff.

## 3. Architecture and contracts

Proposed repository layout:

| Path | Responsibility |
| --- | --- |
| `apps/desktop/{main,preload,renderer}` | Electron lifecycle, narrow validated IPC; React Actions/Flow/Values/Runs and settings |
| `packages/{schema,engine,geometry}` | Versioned contracts, pure validation/control flow, target transformations and sampling |
| `packages/{recorder,project-store,evidence}` | Semantic extraction, immutable commits, media references and trace ingestion |
| `packages/providers/{cli,jev,openrouter}` | Capability discovery, bounded requests, cancellation and typed errors |
| `native/{capture,recognition,input-guard,device-broker}` | Native processes; only input-guard can dispatch |
| `integrations/gnome`, `fixtures/desktop`, `tests/{contracts,scenarios,packaging}` | Compositor bridge, ground truth, acceptance suites |

Keep renderer sandboxed with context isolation and no Node integration; preload exposes operations rather than arbitrary IPC/file access. Main supervises a Node engine utility process. Slow recognition/media/provider work never shares the input guard's event loop. [Electron security guidance](https://www.electronjs.org/docs/latest/tutorial/security).

Versioned JSON messages over inherited local pipes carry request IDs and cancellation IDs; large pixels use bounded shared buffers or asset handles. Reject unknown protocol versions and oversized payloads. Core contracts:

```ts
Observation = { id, sourceId, sourceEpoch, monotonicNs, uncertaintyNs,
  frameHash, dimensions, transforms, focusIdentity }
LocateResult = Found | Absent | Ambiguous | Unavailable
Found = { observationId, candidates, method, score, calibrationId }
DispatchPermit = { runId, revisionHash, actionInstanceId, observationId,
  sourceEpoch, deadlineNs, finalPoint, geometryHash }
DispatchResult = Rejected | Acknowledged | OutcomeUnknown
ValueRef = { producerId, outputId, type }
```

These are contract sketches, not application code. Use JSON Schema plus generated TypeScript/native validators. A result's score is method-specific, never a universal probability. Engine issues single-use permits only after validation; guard checks cancellation, epoch, deadline, focus and input ownership immediately before dispatch. Expiry or reconnect invalidates queued permits.

Session lifecycle: idle → selecting/authorizing → recording **or** preparing-run → acquiring-ready-state → executing/checking → completed/failed/paused → releasing. One recorder/run owns the desktop lease. Repeated start is rejected visibly. Abort invalidates the lease first, then cancels tasks, releases held buttons/keys and finalizes evidence. Guard watchdog releases owned inputs if engine heartbeat disappears; restart never resumes unknown input automatically. Hard guard/compositor failure remains an explicit tested limitation, not a guaranteed release mechanism.

Preflight freezes the revision and checks structural validity, required capabilities, asset availability, output destinations and debug capacity. Pause releases held inputs; resume begins with fresh readiness/location checks. Recommend configurable start/pause shortcuts and a separately reserved abort chord; select actual bindings through D6 conflict tests. A backend with no working out-of-focus abort route cannot pass release acceptance.

Attach selects an existing window/source. Launch is a separate Steam request using a locally verified application identifier/launch entry; do not guess Last Z's ID. Launcher success means only request acceptance. Resolve candidate windows, request user selection when ambiguous, then evaluate a configured visual ready condition. Record launch, window resolution and readiness as separate stages; timeouts distinguish each failure.

## 4. Recording, document and persistence

Capture events and video against a shared monotonic session clock. Preserve original event/server/media timestamps, calibration offset/drift and uncertainty; explicitly handle X timestamp wrap and suspend/source discontinuities. Video PTS maps through a recorded clock model, never frame-number arithmetic. Retain nearest pre/post frames and mark unavailable associations.

Extract clicks from paired down/up events, double-clicks from explicit grouping, drags from button-held paths, wheel actions from scroll packets, and keys from code/modifier/layout transitions including repeats. Short clicks remain events even without an intervening video frame. Preserve raw data so grouping is editable. Do not pretend physical keys reveal IME text; offer explicit text actions. Preserve deliberate holds/drag duration as editable semantics; never add timing randomness or blindly replay idle gaps.

Recording and execution are mutually exclusive, including re-recording. Drain queues between leases. Tag dispatch IDs, exclude broker-owned synthetic devices where applicable, and reject unsupported overlap; do not rely on a universal synthetic-event marker. This prevents our playback becoming a new demonstration.

Recommend `project.json` pointing to immutable `revisions/<hash>.json`, `takes/<id>/events.jsonl`, chunked VP8/WebM video, `assets/<sha256>`, and `runs/<id>/`. Use lossless PNG for recognition/debug frames. A single writer commits assets/revisions before atomically advancing the manifest, with fsync ordering and crash-recovery tests. Save source takes unchanged. Undo/redo switches revision ancestry; new edits fork history. Migrations write a validated new revision with backup; future schemas open read-only. Export materializes all referenced assets with checksums; imports validate paths and sizes. No executable scripts in projects.

Use an ordered AST: sequence, action, wait, if/else, explicit join. Sections group linear actions; inserting a branch splits the grouping without modifying source media (D5). Flow draws this structure; Actions edits it; Values shows its data dependencies. Branch output availability uses definite-assignment analysis: a consumer after a join requires producers on every incoming path or an explicit typed default/optional guard. False is a valid completed condition; unavailable capture/OCR/provider results are errors. Recovery blocks are explicit, bounded and cannot create hidden cycles.

Independent duplication deep-copies executable nodes, targets, outputs and recovery blocks, assigns fresh IDs and remaps internal references. Preserve external references only when valid in the destination; flag others. Immutable footage can be shared. Replacement stages a new take against a selected linear range, then shows actions and affected consumers. Explicitly retain/remap missing producers or save an invalid draft that cannot run. Commit replacement and reference repairs atomically; never map by index/timestamp alone. Undo restores the previous revision, including branches and bindings.

D5 output proposal: run-local strings/numbers/booleans/image handles; explicit parsing and formatted reuse in text/conditions. Save UTF-8 text, JSON or PNG into a user-selected output directory using create-new filenames containing run/action identity. No silent overwrite, append or cross-run values. Those modes need explicit path, collision, transaction and retry decisions; file failure is an action failure, and external file writes are not undone by editor undo.

Stage file contents beside their destination, fsync, publish without replacement, then journal the final path/hash. A crash after publication requires reconciliation against that identity rather than writing another copy. Reject traversal/symlink escapes from the selected directory. Persisted extracted values remain run evidence, not automatically available as inputs to later runs.

## 5. Recognition and guarded clicks

Recommend Tesseract word boxes plus exact/normalized text matching, and OpenCV normalized template matching with a bounded scale pyramid. Retain candidate lists and runner-up separation; evaluate stylized Last Z text, repeated icons, badges, movement and absent targets against labeled fixtures before adoption. OCR neural inference remains local and non-generative. [Tesseract outputs](https://tesseract-ocr.github.io/tessdoc/Command-Line-Usage.html), [OpenCV matching](https://docs.opencv.org/4.x/d4/dc6/tutorial_py_template_matching.html).

Targets distinguish fixed client position, user region, OCR and image. Fixed/region targets still require valid source identity/focus and fresh geometry; they do not imply visual recognition. Search area, located bounds and user-confirmed safe area are separate. Begin with target-relative rectangles and rectangular exclusions. Transform crop → frame → client/compositor → dispatch coordinates using explicit matrices; stream size need not equal compositor size. [Portal coordinate semantics](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html).

Intersect safe geometry with target/client bounds, subtract exclusions, apply inset, then compute eligible integer dispatch points whose inverse-mapped positions remain inside. Sample uniformly from finite scanline intervals using a recorded local seed/algorithm; no retry delays or timing changes. Record seed, chosen point, transforms and geometry hash. Empty geometry fails. Fixed clicks stay fixed unless the user supplies a safe region. Source/scale/focus changes invalidate geometry; never reuse old window handles blindly.

Reacquire after waits, pause, recovery and online judgments. Require observation age **plus** timestamp uncertainty within the approved budget and recheck target position against the newest frame before dispatch. Excess movement or ambiguity restarts bounded location. This reduces, but cannot eliminate, the screen-change race between observation and input.

D6 experimental profile: target timeout 10 seconds, launch readiness 60 seconds, checks every 100 ms, dispatch freshness 150 ms, two consistent target observations, 30 fps reference video. Calibrate against fixture/game measurements before approval. No generic numeric OCR/image/AI threshold: derive per-method thresholds and ambiguity margins from labeled negatives, then approve and persist calibration versions. Expired checks stop and explain by default; explicit pause/recovery policies use fresh observations.

## 6. Debugging and optional providers

Every debug action/check/locator attempt records raw pre-frame, candidates, OCR/parsed values, attempted operations and post-frame/result. Keep overlays separate: search/target/safe regions, exclusions and final click. Deduplicate frames by hash, not timestamps. Trace identity includes immutable revision, source references, engine/helper/provider versions, calibration and monotonic sequence. Hash-chain journal records and verify frame hashes to detect corruption; this is not an authenticity claim.

Persist dispatch intent and required pre-frame before authorizing input; append acknowledgment and post-observation separately. A crash between these records yields **outcome unknown**, never automatic replay/retry. Missing frames have explicit reasons. Debug disk exhaustion fails closed before the next dispatch; if already dispatched, stop, release inputs and show incomplete evidence. Reserve a small emergency journal, but acknowledge it too can fail; infer incomplete traces on restart. Non-debug runs still record basic outcomes.

Runs offers chronology, branches, attempts, before/after comparison, overlays, values and navigation to the historical editor revision. Replay has no input capability; starting another run is a separate command. D6 proposal: no automatic deletion initially, warn at a configurable budget, stop recording/debug capture when the budget is exhausted. Choose numeric budgets after storage measurement. Retention, selective pruning, redaction and export require approval; prune only unreferenced assets. Export preview lists selected frames/values; credentials never enter traces.

Offline acceptance blocks outbound networking and verifies recording, edit/save, OCR/image targeting, branches, outputs and Runs; package fonts, OCR data and all runtime assets. Core never spawns a provider. Updates and provider traffic are explicit.

Provider settings show executable/version, authentication readiness, selected model, text/image/schema capabilities and billing category. Recommend bounded subprocess requests: `codex exec --json` with output schema; `claude -p` with JSON/schema output. Use installed **unmodified** binaries and CLI-owned login; never read/copy saved tokens. Codex documents saved-auth reuse and distinct subscription/API authentication. Claude's product-integration conditions require its published binary and each user's own authentication; eligibility remains account/use dependent. [Codex execution](https://learn.chatgpt.com/docs/non-interactive-mode), [Codex auth](https://learn.chatgpt.com/docs/auth), [Claude CLI](https://code.claude.com/docs/en/headless), [Claude integration conditions](https://code.claude.com/docs/en/legal-and-compliance).

Contract: request ID, revision/observation ID, deadline, capability requirements and schema → validated judgment/proposed edit or typed auth/limit/unsupported/malformed/transport/cancel error. Spawn argument arrays in an app-owned scratch directory, cap streams, deny desktop tools and workflow mutation, and verify documented settings isolate inherited hooks/plugins. Claude bare mode excludes subscription login; do not use it to satisfy this requirement. If invocation isolation cannot preserve eligible login, D8 reopens. Cancel releases desktop control immediately, interrupts the process, then terminates its process group after bounded grace; discard late responses. No silent API fallback. [Claude lifecycle and bare-mode behavior](https://code.claude.com/docs/en/headless).

Jev stays explicitly online/experimental: send only selected crops with transform metadata; request presence and uniqueness alongside Box, validate normalized bounds and retain evidence. Box returns coordinates even when absent, so Box alone never permits input. Revalidate current local screen after network latency; if freshness cannot be established, stop. OpenRouter implements the same capability/error contract later, with separate credentials/billing and no subscription inference. [Jev images](https://docs.typesafe.ai/images), [Box/presence](https://docs.typesafe.ai/primitives/box).

For a delayed visual judgment, require either identical relevant pixels in a fresh observation with unchanged transforms or a fresh local locator that independently confirms the target. Record that verification observation separately. Animated regions may fail this deliberately conservative policy; measure whether Jev remains useful before approving its click capability. Do not simply reset the old judgment's timestamp.

## 7. Ordered work packages and release evidence

| ID; dependencies | Deliverable | Observable acceptance |
| --- | --- | --- |
| A00; D1–D4 | Approved architecture records; fixture and capability harness | Six independent capability reports per backend; failed Wayland cells trigger reconsideration, not support claims. |
| A01; A00, D3/D5 | Schema, immutable store, migrations, AST validator | Crash recovery, round-trip migration, branch availability, ID-remapping and replacement/undo scenario tests. |
| A02; A00–A01 | Recorder, synchronization model, semantic extractor | Fixture ledger matches short clicks/keys/drags; video association uncertainty visible; no synthetic feedback. |
| A03; A01–A02 | Guarded offline engine, fixed/image targets, minimal editor and trace writer | Record→edit→reload→run fixture; absent/stale/ambiguous target sends no input; abort releases held inputs. |
| A04; A03, D5 | Approved Actions/Flow/Values UI, OCR, branches, replacement, file outputs | Cross-view identity agrees; independent copy stays unchanged; both branches and missing producers exercised. |
| A05; A03–A04, D6 | Runs, recovery policies, integrity/retention controls | Disk-full/crash/source-loss evidence understandable; historical revision survives edits; replay dispatch count is zero. |
| A06; A05, D8 | CLI settings/adapters, Jev, provider contract fixtures | Fake-process error/cancel tests first; later authorized real account/model tests demonstrate declared capabilities only. |
| A07; A04–A06, D7 | Packages, dependency inventory, support matrix, release documentation | Clean-machine install and full offline journey on both approved sessions; Last Z attach/launch/readiness plus chosen routine and Chrome regression. |

Property tests cover transforms/rounding/exclusions and reference remapping; scenario tests cover real failures, not implementation-shaped mocks. Measure input-to-frame skew, abort latency, dropped events/frames, capture CPU/RAM, recognition latency, startup time, bytes per recording minute/action and Runs seek time at growing history sizes. Report distributions and hardware; approve budgets from evidence, then use them as regressions.

The clean-machine matrix names distro, compositor/session, portal backend, GPU/driver, scaling, input layout and package versions. Include denied/revoked permissions, multiple windows, source replacement, unplugged monitors, full disk and process termination. Fixture scenarios must cover fixed/region/OCR/image targets, both branch outcomes, text/screenshot reuse, independent copies, partial replacement/undo, stop/pause/recovery and debug replay. Last Z testing begins only after those gates, using a user-selected minimal routine; no assumed Mail workflow.

Critical path: D4 passive observation/coordinates → synchronized recorder → guarded executor → recoverable evidence → packaged dual-session acceptance. Recognition and CLI feasibility can be investigated independently without delaying the offline slice. Public release requires both X11 and the explicitly tested Wayland combination; X11-only builds may be labeled development previews. Publish build instructions, exact dependency/model licenses, checksums, migration/backup guidance and capability diagnostics. Windows/macOS get later adapters against the same contracts, with their own acceptance evidence.
