# Record to Automate: implementation plan (Opus v1)

Author: Claude Opus 5.5 high. This is an independent plan for peer review. Nothing in it is implemented, tested, or approved beyond [DECISIONS.md](../../DECISIONS.md). Each "Recommend" is a proposal, and the decision gate named with it (`D-…`) must be resolved before the related work package starts.

## 1. Status

**Confirmed (not reopened here):** everything in DECISIONS.md. Release acceptance (§11) re-tests each item:

- X11 and Wayland both required.
- Last Z first, Chrome and other apps supported, no game-specific core.
- Automatic video and action recording.
- Fixed, region, OCR, and image targets.
- Independent copies.
- Partial re-record with undo.
- Screen-state conditions in one workflow.
- Text, screenshot, value, and file outputs.
- Offline core.
- Optional user-owned Claude/Codex CLIs, Jev, and OpenRouter.
- Position-only variation.
- Stop-and-explain default.
- Debug replay that never sends input.
- The approved design plus a Runs view.

**Proposed:** everything below. **Decisions required:** §12.

## 2. Architecture

### 2.1 Processes and boundaries

| Process | Owns | Never |
| --- | --- | --- |
| Electron **main** (TS) | Windows, typed IPC broker, project I/O, settings, provider spawning | Recognition, dispatch state |
| **Renderer** (TS, React + Tailwind, D-UI) | Editor, Runs viewer, Settings | Network, filesystem, or host access; strict CSP and a typed preload only |
| **Engine** (Electron [utilityProcess](https://www.electronjs.org/docs/latest/api/utility-process), TS) | Validation, interpretation, attempts, values, policies, trace writing | Dispatch without a host lease |
| **`r2a-host`** (Rust, D-HOST) | Capture, input observation, dispatch, window discovery, shortcuts, abort, held-input release | Deciding *what* to click |
| **`r2a-vision`** (Rust, D-HOST) | Template matching, OCR, crops, hashes | Dispatch, network |

**Why this split:**

- A crash in native code must not take the editor down or leave a key held.
- Abort must keep working while the UI or engine is hung.
- Workflow logic stays in TypeScript, so the UI, validator, and tests share one set of types.
- Bun runs workspaces, scripts, and tests of pure packages. Electron's Node runs the app.

**Why Rust for the helper (D-HOST):** maintained Rust crates cover X11 (RECORD, XI2, XTEST, XShm), PipeWire, D-Bus portals, and libei, so one memory-safe binary handles all of them, and later Windows and macOS adapters can share the core.

The alternatives are weaker:

- An N-API C++ addon crashes in-process and needs an ABI rebuild for each Electron release.
- Pure Node has no PipeWire or libei bindings.
- Go still needs cgo.

**Cost:** contributors need a second toolchain. CI ships prebuilt helpers to offset it.

### 2.2 Host-enforced safety

The engine cannot dispatch on its own authority; the host checks every command.

1. **Lease.** The engine renews a lease by heartbeat. The host releases all held keys and buttons and rejects further dispatch on any of these:
   - lease expiry or pipe EOF;
   - the abort shortcut;
   - SIGTERM.
2. **Dispatch request** `{leaseId, observationId, sourceDescriptorHash, pointInput, notAfterMonotonicNs, holdMs}`. The host rejects it if the window or source geometry changed, the deadline passed, or the point lies outside the current client rectangle. Each rejection returns a typed result, and the engine traces it.
3. **Feedback isolation.** Recording and running cannot happen at the same time: the host refuses dispatch while recording. Injections also carry sequence numbers. On X11, observed events are also classified by XI2 `sourceid` (G-X2).

### 2.3 Package layout (Bun workspaces)

```
apps/desktop/           main/, preload/, renderer/ (tokens from approved study)
packages/schema/        zod types, canonical JSON, migrations + corpus
packages/engine/        interpreter, validator, dataflow, policies, FakeHost
packages/geometry/      coordinate spaces, transforms, safe areas, seeded sampler
packages/recording/     raw events → semantic actions
packages/trace/         append-only evidence writer/reader, integrity
packages/store/         project I/O, revisions, media CAS, GC, export
packages/host-protocol/ messages shared with Rust (one JSON Schema → TS + Rust)
packages/providers/     claude-cli, codex-cli, jev-http, openrouter (later)
native/r2a-host/        crates: core, x11, wayland, evdev (gated)
native/r2a-vision/      template, ocr, hashing
fixtures/target-app/    deterministic fixture (WP1)
spikes/                 gate programs + REPORTS/*.md
```

### 2.4 Typed contracts (abridged)

```ts
type Id = string; // ULID; never derived from names, never reused
type Node =
  | { kind: 'section'; id: Id; takeId?: Id; steps: Step[] }
  | { kind: 'wait'; id: Id; until: Check; policy: FailurePolicy }
  | { kind: 'if'; id: Id; arms: { when: Check; then: Block }[]; else?: Block }
  | { kind: 'output'; id: Id; write: FileWrite }            // D-FILE
  | { kind: 'stop' | 'pause'; id: Id; message: string };
type Step = { id: Id; action: Action; target?: Target; policy: FailurePolicy;
  produces?: ValueBinding[]; sourceEventIds?: Id[] };
type Target = { id: Id; locator: Fixed | Region | OcrText | Image | Jev;
  searchArea: RectRel; safeArea?: SafeArea; variation: 'none' | 'uniform-in-safe' };
type CheckResult = { status: 'true' | 'false' | 'unknown'; reason?: ErrorCode; evidence: EvidenceRef[] };
type FailurePolicy = { timeoutMs: number; onTimeout: 'stop' | 'pause'
  | { recover: Block; then: 'retry-step' | 'continue' | 'stop'; maxRecoveries: number } };
```

`unknown` is never coerced to `false`. If it persists until the timeout, the step's policy applies.

### 2.5 Control flow and validation

- **Structure.** A workflow is a tree of sequences, if/else-if/else, waits, stops, and pauses, with no cycles. The bounded polling inside an attempt is not a user-facing loop.
- **Inserting a condition into a recorded section** splits it into two sections. Both share the same `takeId` over disjoint step ranges, so the footage stays intact (D-SPLIT).
- **Validator.** It runs on every edit and checks:
  - **References:** every reference resolves by ID.
  - **Definite availability:** a value is usable at node *N* only if every path to *N* passes through a producer. A consumer of a value made in only one arm is an error unless it sits in that arm or declares a fallback.
  - **Types and geometry:** types match, and every target has geometry.
  - **Recovery:** recovery cannot recurse.
  - **Online use:** online steps are flagged.
- **Invalid documents** save but cannot run, and the editor names the blocking nodes.

### 2.6 Persistence, media, versioning (D-STORE)

Recommend a file-canonical project directory. Indexes can be rebuilt from it. It needs no native database module in Electron, it can be inspected and versioned in Git, and atomic renames make it crash-safe.

```
MyWorkflow.r2a/
  project.json              schemaVersion, appVersion, head revision
  revisions/<sha256>.json   immutable canonical JSON (RFC 8785)
  history.ndjson            undo/redo chain
  takes/<takeId>/           immutable: video, events.ndjson, clock.json
  runs/<runId>/trace.ndjson append-only, hash-chained
  media/sha256/…            lossless stills/crops, content-addressed
```

- **Three separate records:** takes (what was demonstrated), revisions (edited intent), and runs (what actually happened). They are stored apart, each is immutable once written, and they are linked only by ID.
- **Save:** write the revision file, fsync it, then atomically replace `project.json`.
- **Undo:** moves `head` back to an earlier revision.
- **Garbage collection:** blobs not reachable from retained revisions, takes, or runs go to quarantine first.
- **Migrations:** pure `vN→vN+1` functions, tested against a corpus of real older files. Before the first write, the original file is backed up. Projects from a newer major version are refused, with an explanation.
- **Traces:** never migrated. The viewer keeps a reader for each version.
- **Alternative: a SQLite project file.** It offers transactions but is opaque and needs a native module. Reconsider it if measurements show problems with file counts.

## 3. Runtime lifecycle

Runs move through `preparing → acquiring-app → running ⇄ paused → completed | stopped(explanation) | aborted | failed-internal`.

**Step attempt:**

1. **Observe.** Take a frame newer than both the previous step's settle point and `maxObservationAge` (D-NUM).
2. **Locate.** Collect candidates and their method-specific scores.
3. **Decide.** No candidate means *missing*; more than one within an ambiguity margin means *ambiguous*. Both keep polling until the timeout.
4. **Plan the click point** (§5).
5. **Confirm.** Recapture and compare the located region by normalized cross-correlation or hash. If it changed, go back to Observe; the time spent counts toward the same timeout.
6. **Dispatch through the lease.** The `dispatch.intent` record is fsynced first.
7. **Settle.** Wait for the explicit post-condition, or a fixed settle delay.

**Delays** are fixed and traced; none is randomized.

**Timeout policies:**

- **stop** (default). Emits `{code, stepId, expected, observed, evidenceRefs, suggestion}`.
- **pause.** The run waits for the user. Resuming always re-observes the screen.
- **recover.** Runs the recovery block, then retries, continues, or stops, limited by `maxRecoveries`.

**App acquisition.** Each phase has its own timeout and error message.

- **Attach** matches a stored *window rule*, never a window ID: WM_CLASS (for example `steam_app_<id>` under Proton), a title pattern, or PID ancestry. On Wayland it uses the portal picker plus a restore token.
- **Launch** moves through `launch-requested` (`steam -applaunch <appid>` / `steam://rungameid/<id>`), then optional `process-seen`, `window-matched`, `window-stable` (geometry unchanged across frames), and `ready-observed` (a user-defined screen check). A launch request never implies readiness.

## 4. Recording

**Frames.** The host captures frames and stamps each one with `CLOCK_MONOTONIC`. Frames feed two outputs:

- a scrubbing video (VP9/AV1, which avoids H.264 licensing questions; see D-PKG);
- a short lossless ring buffer. For each action, the *pre-frame* is the last frame before press time minus the clock uncertainty. It is stored losslessly together with the press and release frames, because template crops must not come from compressed video.

**Clock synchronization:**

- **X11:** RECORD delivers server-millisecond timestamps. Every few seconds, the host stamps a self-generated `PropertyNotify` round-trip with monotonic `t0` and `t1`. It stores `{offset, uncertainty=(t1−t0)/2}` in `clock.json`.
- **Wayland:** PipeWire buffer timestamps and evdev (with `EVIOCSCLOCKID` set to monotonic) can share one clock. The gates must show this works rather than assume it.
- **Measurement:** the fixture renders a visual timecode, so synchronization error is measured, not asserted.

**Semantic extraction** is pure TypeScript, tested against raw-event fixtures. Thresholds are set under D-NUM.

| Raw input | Extracted action |
| --- | --- |
| Press and release within a distance threshold | Click |
| Two clicks inside the platform interval | Double click, replayed at a fixed interval |
| Press, move past the threshold, release | Drag: a simplified polyline with endpoint targets |
| Printable keys in a row | Type text |
| Modifier chord | Shortcut |
| Key held past a threshold | Hold |
| Wheel steps | One aggregated scroll |

Events on the recorder's own windows, and the stop shortcut, are dropped. Each action keeps its source event IDs and a default image target cropped from its pre-frame, with a fixed fallback.

## 5. Targets, coordinates, safe clicks

**Coordinate spaces:** `frame → client → desktop (X11 root or Wayland stream-logical) → input`.

- **Descriptors.** Every transform carries a `sourceDescriptor`: monitor or window, size, scale, offset, and stream node.
- **Geometry changes** (`ConfigureNotify`/RANDR, PipeWire parameter changes) create a new descriptor. Observations bound to the old one become stale, and the host rejects dispatches that reference them.
- **No hardcoded offsets.** Screenshot title-bar offsets are never hardcoded.

**Geometry layers:**

1. **Search area:** client-relative, normalized, plus the recorded pixel size.
2. **Located box.**
3. **Safe area:** a polygon in *target-local normalized* coordinates, so it scales with the box. Exclusions are removed from it, then it is eroded by a margin in input pixels.
4. **Click point.**

A fixed locator with no safe area clicks exactly the transformed recorded point.

**Sampling.** The seed for each run is traced, so sampling can be replayed offline.

1. Sample uniformly inside the eroded polygon, in continuous input space.
2. Round to integer pixels.
3. Accept only if the *rounded* pixel centre is inside both the eroded polygon and the current client rectangle.
4. Retry a bounded number of times, then fall back to the pole of inaccessibility.
5. If even that is invalid, stop with `safe-area-empty`.

The Actions view's "preview variation" scatter calls this same sampler. fast-check property tests generate random polygons, scales, exclusions, and rounding cases and assert that every point stays contained. Variation never changes timing and is not presented as a way to evade detection.

## 6. Editing operations

**Independent duplicate.** The copy is a deep clone in which a full remap table gives every step, target, node, and binding a new ID.

- References inside the copy are rewritten to point at the copy.
- References to values outside the copy are kept and revalidated at the new location.
- Immutable takes and media are shared.
- A test asserts that editing one copy leaves the other byte-identical.

**Partial re-record.**

1. Select a contiguous range in one section. The app shows the first step's pre-frame as "get the app to this screen" and offers a similarity check.
2. Record a candidate take.
3. The app proposes an old-to-new mapping from action kind and target similarity: old crops matched in new pre-frames, and OCR text equality. Timestamps and list position are never used for mapping.
4. The user confirms the mapping in the approved compare panel.
   - Mapped steps **keep their IDs**, so downstream references survive.
   - New steps get new IDs.
   - Producers that disappear list their consumers. The user can keep the old extraction, rebind the consumers, or accept a non-runnable state.
5. Accepting creates one atomic revision. Undo restores the previous revision. The old take is retained.

**File outputs (D-FILE).** Recommend an explicit Output node.

- **Modes:**
  - `overwrite`: temp file, fsync, rename;
  - `append-line`;
  - `append-csv`: header written once;
  - `new-file-per-run`.
- **Location:** files go only inside a folder the user granted.
- **Values:** run-local; persistent values wait for D-VALUES.

## 7. Platform feasibility gates

**Per-gate deliverables:** a spike program plus `spikes/REPORTS/<gate>.md` with commands, an environment fingerprint, raw logs, and fixture ground truth.

**Timebox rule:** one focused attempt per candidate technique. A candidate is marked *failed* after two failures that are not attributable to our own bug, and the gate escalates as listed.

**Evidence standard:** no combination is claimed without a passing report. The XRECORD, XTEST, XInput, and RANDR extensions present on this machine show only that they are available, not that they work for us.

### X11: current machine, starting immediately

| Gate | Proves | Acceptance evidence | On failure |
| --- | --- | --- | --- |
| G-X1 Pixels | XShm window/root capture with monotonic stamps; fullscreen Proton | Fixture timecode decoded; CPU/fps measured; Last Z frame correct | XComposite pixmap; measured `ffmpeg x11grab` fallback |
| G-X2 Passive observation | [RECORD](https://www.x.org/archive/X11R7.5/doc/Xext/record.pdf) events with root coordinates, plus XI2.1 raw events, which reach root "regardless of the grab state", with `sourceid` ([XI2](https://xorg.freedesktop.org/archive/current/doc/inputproto/XI2proto.txt)) | Fixture and Last Z still receive all input; coordinates equal the fixture log; injected events are separable by source device (an Xorg implementation detail that must be demonstrated) | Rely on mode exclusivity and the host's injection sequence |
| G-X3 Dispatch | [XTEST](https://www.x.org/releases/current/doc/xextproto/xtest.pdf) click, drag, key, and hold into the fixture, Chrome, and a harmless Last Z element the user designates | Fixture log equals the commands; the user confirms the game reacted | uinput (privileged, D-PRIV) before reconsidering |
| G-X4 Transforms | Window move/resize, multi-monitor offsets, scaling | Commanded point equals the fixture-reported point after rounding, across the matrix | Fix the model; never patch with offsets |
| G-X5 Shortcuts and abort | `XGrabKey` while the game is focused/fullscreen; abort on detected physical input; release held input when the engine or UI is killed | Interrupted drag leaves no held button; abort works over the game | Physical-motion abort as primary, tray as secondary |

### Wayland: VM or separate install, never altering the current session

| Gate | Proves | Acceptance evidence | On failure |
| --- | --- | --- | --- |
| G-W0 Environment | D-WAY candidate; portal interfaces and versions the installed backend actually exposes | `busctl` introspection captured | Next candidate |
| G-W1 Pixels | [ScreenCast](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html) window and monitor streams; `cursor_mode` metadata (cursor "sent as PipeWire stream metadata"); restore tokens | Timecode decoded; cursor metadata matches the fixture pointer log; second session needs no picker | Monitor stream plus crop |
| G-W2 Passive observation | Candidates: **W-a** XWayland-scoped RECORD/XI2 (Proton usually runs on XWayland; Chrome may not); **W-b** evdev button/key reads (`input` group, D-PRIV) with ScreenCast cursor position; **W-c** compositor integration such as a GNOME Shell extension | Apps still receive input; positions map to frames; coverage table per app and event type | [InputCapture](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.InputCapture.html) is excluded: events go "directly to the application instead of" the pointer, and capture is triggered at barriers. If nothing covers Last Z plus Chrome, present options and consequences to the user. Never silently switch the recording UX or drop Wayland |
| G-W3 Dispatch | [RemoteDesktop](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.RemoteDesktop.html) absolute pointer events relative to a stream; `ConnectToEIS` (interface v2); Xwayland XTEST via libEI ([Xwayland 23.2](https://lists.freedesktop.org/archives/xorg/2023-July/061397.html)) | Fixture log equals the commands; Last Z reacts; prompts documented | Mark that compositor unsupported for release |
| G-W4 Transforms | Stream-logical vs input coordinates at scales 1.0, 1.25, and 2.0 | Commanded point equals the reported point | Fix the model |
| G-W5 Shortcuts and abort | [GlobalShortcuts](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.GlobalShortcuts.html) (user-assigned trigger; read `trigger_description`); evdev hotkey if W-b is used; devices released on session close | Abort works over the game; nothing held after an engine kill | Tray/notification abort plus physical-input detection; limitations documented |

**Portal versions.** The 22.04-era distributions probably ship a portal older than 1.17, the release that introduced libei/InputCapture. G-W0 records what is actually installed, and GNOME 42 Wayland gets its own report row.

**D-WAY recommendation:**

- **First:** current GNOME on an LTS distribution (candidate: Ubuntu 26.04 LTS, confirmed in G-W0). It is closest to the user's GNOME setup, and Mutter implements ScreenCast and RemoteDesktop.
- **Next:** KDE Plasma 6.
- **Then:** COSMIC, the Pop!_OS 24.04 desktop.

## 8. Recognition

**Locator stack** (deterministic, tried in order):

1. Fixed and region locators.
2. Multi-scale normalized cross-correlation for images (top-k candidates with non-max suppression).
3. OCR for text.

**OCR engine (D-OCR).** Choose by evaluation between:

- [Tesseract](https://tesseract-ocr.github.io/tessdoc/Command-Line-Usage.html) (Apache-2.0, bundled models, fixed thread count);
- a non-generative ONNX OCR model such as PaddleOCR detection and recognition.

**Evaluation G-R1:**

- **Corpus:** at least 200 labeled Last Z frames captured by the user during the gates (loading, popups, menus, resized windows, absent and repeated icons), plus fixture scenes.
- **Metrics:** hits, misses, *wrong-target rate*, ambiguity detection, and latency by search-area size.
- **Adequacy rule:** a method is adequate for a target class only if its wrong-target rate on the corpus is zero at its operating point.
- **Thresholds:** stay method-specific per target ([template matching](https://docs.opencv.org/4.x/d4/dc6/tutorial_py_template_matching.html)).

## 9. Offline guarantee and optional AI

**Offline enforcement:**

- The renderer CSP blocks remote origins, and main denies non-app requests.
- A lint rule allows network and AI child-process imports only in `packages/providers`.
- OCR models are bundled.
- CI runs the full end-to-end suite under `unshare -n`.
- Online steps are badged, both on the step and when a run starts.
- Offline workflows never spawn a provider.

**CLI adapter contract:**

| Operation | Behavior |
| --- | --- |
| `detect()` | Find the binary and its version. Never read credential files |
| `test()` | Run a minimal documented request. Authentication state is inferred only from the CLI's own output |
| `capabilities()` | Image input, structured output, and models, per version and test result |
| `invoke()` | Spawn with an argument array (no shell) in an empty temp directory that holds only the inputs |

- **Timeout:** a fixed limit per request.
- **Cancellation:** SIGINT, then SIGTERM, then SIGKILL to the process group.
- **Validation:** output must pass a zod schema.
- **Errors:** `not-installed | not-authenticated | unsupported-capability | rate-limited | timeout | cancelled | malformed | crashed | policy-refused`.

**Claude Code** (`claude -p --output-format json --json-schema …`):

- **Missing output:** if `structured_output` is absent, the request fails.
- **No `--bare`:** bare mode "doesn't use your subscription login" ([headless](https://code.claude.com/docs/en/headless)). Non-bare mode loads the user's `~/.claude` configuration; Settings discloses this.
- **Product conditions:** Anthropic requires an unmodified binary and user-owned authentication, and references Commercial Terms for products ([legal](https://code.claude.com/docs/en/legal-and-compliance)). D-AIELIG must resolve this before subscription use is advertised.

**Codex** (`codex exec --json --output-schema <file> --sandbox read-only --ephemeral --skip-git-repo-check`, with `-i` for images):

- It reuses saved CLI authentication ([non-interactive](https://learn.chatgpt.com/docs/non-interactive-mode), [reference](https://developers.openai.com/codex/cli/reference)).
- **First-release scope:** both CLIs provide editing assistance only. They propose typed edits, which are validated and applied as one undoable revision. Runtime judgments through a CLI come later and are marked experimental.

**Jev** (experimental; its docs say it is not "ready for production"):

- **Image preparation:** crop the search area, scale it so the long side is at most 1568 px, pad it to at least 280 px, and record the transform ([images](https://docs.typesafe.ai/images)).
- **Box results:** Box "has no 'not found' answer" ([Box](https://docs.typesafe.ai/primitives/box)), so every request also includes a presence question.
- **Acceptance:** a candidate is accepted only if presence meets a user-tuned threshold, the box is inside the search area, and its size is plausible. It then passes the same safe-area and confirm steps as local candidates.
- **Key storage:** the Jev API key goes in Electron `safeStorage`.

**OpenRouter:** an `HttpModelProvider` that implements `JudgmentProvider`, behind a feature flag (D-OR).

## 10. Debug evidence and Runs viewer

**Trace records:** `{seq, tsMono, tsWall, kind, runId, revisionId, nodeId, attempt, payload, prevHash}`, hash-chained. The header holds the app version, capability report, seed, and settings.

**Record kinds:**

| Kind | Contents |
| --- | --- |
| `observe` | Frame, descriptor, age |
| `locate` | Candidates, scores, OCR text |
| `decide` | Located, missing, or ambiguous |
| `point` | Safe area, exclusions, sample attempts, final point, transform |
| `confirm` | Before-dispatch recapture result |
| `dispatch.intent`, `dispatch.result` | Accepted, or rejected with a reason |
| `settle` | Post-action frame |
| `value`, `branch`, `policy` | Value produced, branch taken, policy applied |
| `provider` | Provider and outcome, without credentials |
| `end` | Final outcome |

**Integrity rules:**

- An `intent` with no matching result is shown as "attempted, outcome unknown".
- A missing blob is shown as "frame not stored" with the reason.
- A trace with no `end` is shown as "incomplete".
- A broken chain is flagged.

**Disk.** Free space is checked against a reserve before the run (D-NUM). A failed write or crossing the reserve applies D-RETAIN.

- **Recommended default:** stop and explain, matching the failure default.
- **Option:** continue with a text-only trace and visible gaps.
- **Always recorded:** basic status and error events.

**Runs viewer** (approved visual language):

- **Run list:** outcome, revision, duration.
- **Chronology:** a rail of nodes and attempts, showing the actual branch.
- **Frame stage:** raw/overlay toggle for search area, candidates, safe area, exclusions, and click point.
- **Comparison and values:** pre/post comparison, and values at that moment.
- **"Open in editor":** maps by stable ID and warns when the run's revision is not the current head.
- **No input:** replay only renders evidence. The viewer has no IPC route to the host.

## 11. Work packages

| ID | Depends on | Deliverable | Acceptance evidence |
| --- | --- | --- | --- |
| WP0 Scaffold | — | Workspaces, Electron shell with CSP, Rust crates, CI (unit, Xvfb, `unshare -n`) | CI green; app launches on X11 |
| WP1 Fixture | WP0 | Deterministic scenes (moving and repeated targets, delayed readiness, popups, counters, decoys, absent target), timecode, JSONL input log, also served for Chrome | Scene and log round-trip test |
| WP2 X11 gates | WP1 | G-X1–5 reports | §7 |
| WP3 Wayland gates | WP1, D-WAY | G-W0–5 reports | §7, or escalation to the user |
| WP4 Recognition evaluation | WP1 | Corpus tool, G-R1 report | Metrics table |
| WP5 Steam acquisition | WP2 | Launch/attach/readiness spike | Phases distinguishable in logs |
| WP6 Schema and store | WP0, D-STORE | Schema, store, migrations, GC | Kill-during-save never loses head; migration corpus passes |
| WP7 Host X11 | WP2 | Adapter, lease, protocol | Stale, expired, and out-of-rect commands rejected; kill releases inputs |
| WP8 Geometry | WP0 | Transforms, sampler, preview | Property tests |
| WP9 Vision | WP4 | Template matching and OCR | Corpus regression in CI |
| WP10 Recording | WP6, WP7 | Takes, clock calibration, extraction | Fixture session gives the expected actions; sync error measured |
| WP11 Engine | WP6–WP9 | Interpreter, policies, FakeHost, trace | Golden traces: missing, ambiguous, stale, unknown≠false, re-observe on resume, recovery bound |
| **Slice A** | WP10, WP11 | X11: record fixture → save/reload → run fixed and image targets → stop-and-explain with debug frames | End-to-end on Xvfb and a real session |
| WP12 Actions view | Slice A, D-UI | Approved Actions UI, target editor, variation preview | Fixture walkthrough; parity with the study |
| WP13 Flow and Values | WP12 | Conditions, waits, validator UI | Selection matches across views; rename preserves bindings |
| WP14 Copy, re-record, undo | WP13 | §6 operations | Independence and replacement tests |
| WP15 Outputs | WP13, D-FILE | OCR outputs, screenshots, Output node | Mode tests, including a crash during overwrite |
| WP16 Runs viewer | WP11, WP12 | §10 viewer | Incomplete and corrupt traces render; replay sends zero host messages |
| WP17 Host Wayland | WP3, WP7 | Adapter per gate outcome | X11-equivalent suite passes on the chosen compositor |
| **Slice B** | WP12–WP17 | User-chosen Last Z routine with conditions and debug, on X11 and Wayland | User-witnessed runs and traces |
| WP18 Providers | WP11, D-AIELIG | Claude/Codex assist, Jev, OpenRouter stub | Fake-CLI contract tests (timeout, malformed output, cancel); user-run smoke test with their own login |
| WP19 Release | Slice B, D-PKG, D-LIC | AppImage and .deb, `r2a doctor`, notices, docs | Clean-machine matrix |

**Critical path:** WP1 → WP2/WP3 → WP7/WP17 → WP10 → WP11 → Slice A → WP14 → Slice B → WP19. WP3 starts immediately, because Wayland results can change the observation and coordinate model.

**Verification:**

- **Pure packages:** unit and property tests.
- **Engine:** deterministic tests against FakeHost.
- **X11:** Xvfb in CI.
- **Wayland and real hardware:** a scripted lab checklist for each release.
- **Evidence:** every acceptance claim cites a log, trace, or report.

**Support matrix:**

| Environment | Status |
| --- | --- |
| Pop!_OS 22.04 X11 | Primary |
| Clean Ubuntu 22.04 X11 VM | Required |
| Chosen Wayland compositor (VM and bare metal) | Required |
| GNOME 42 Wayland | Report only |
| Windows, macOS | Later |

**Measured, not promised:**

- capture CPU and fps;
- encoding MB/min;
- bytes per action and per debug step;
- locate latency;
- observation-to-dispatch age;
- abort-to-release latency.

These measurements set the D-NUM defaults.

**Release:** a public repository with CI-built, checksummed artifacts, license notices, and `r2a doctor` output in bug reports. Release acceptance demonstrates every §1 requirement on both X11 and the chosen Wayland compositor.

## 12. Decision gates

| ID | Decision | Recommendation |
| --- | --- | --- |
| D-HOST | Helper language and process model | Rust host and vision processes with lease safety |
| D-WAY | First Wayland compositor and acceptable setup | Current GNOME on an LTS distribution; KDE Plasma 6 next |
| D-PRIV | Privileged paths (input group, uinput), if required | Opt-in only, with a visible recording indicator |
| D-UI | Renderer framework | React + Tailwind + React Aria |
| D-STORE | Project format | File-canonical, content-addressed media, NDJSON traces |
| D-OCR | OCR engine | Decided by G-R1 |
| D-NUM | Timeouts, polling, freshness, thresholds, margins, disk reserve | From measurements; per-step override |
| D-SPLIT | Condition insertion splits sections | Accept |
| D-FILE / D-VALUES | File modes; value persistence | §6 modes; run-local values |
| D-RETAIN | Retention, budget, exhaustion, redaction, export | Keep failures plus the last N runs; stop on exhaustion; export opt-in |
| D-AIELIG | Claude/Codex subscription eligibility | Verify current terms before advertising |
| D-OR | OpenRouter priority | After Slice B |
| D-LIC / D-PKG | License and packages | Apache-2.0; AppImage and .deb, then Flatpak once a portal-only path is proven |
| D-KEYS | Shortcuts; invocation during an active run | Ignore with a notice; abort always wins |

**Highest risk:**

1. **D-WAY + D-PRIV:** Wayland passive observation may require privileges or compositor integration.
2. **D-HOST:** fixes the safety and process architecture.
3. **D-AIELIG:** subscription use through the user's own CLI must match current terms.

**Other risks:**

- Last Z may reject synthetic input, or its stylized text may defeat OCR. G-X3 and G-R1 check both before UI work.
- Clock skew could misattribute frames. This is mitigated by measured uncertainty and lossless pre-frames.
- Loops, calls, and schedules stay out of scope unless decided.
