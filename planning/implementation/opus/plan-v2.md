# Record to Automate: implementation plan (Opus v2)

Author: Claude Opus 5.5 high, 2 November 2026. Revises `opus/plan-v1.md` after `reviews/astra-on-opus-v1.md` and my review of the peer plan; dispositions are in `opus/responses-v2.md`.

Nothing here is implemented, tested or approved. [DECISIONS.md](../../DECISIONS.md) and [PLAN.md](../../PLAN.md) are the requirements and are not reprinted. Each "recommend" is held by a gate `D-…` (§13). Tags: **[doc]** checked against an official primary source on 2 November 2026; **[obs]** baseline environment record; **[gate]** to be proven. Nothing has been measured. Numbers marked *experimental* are gate test targets, not defaults or accuracy guarantees.

## 1. Binding principles

1. **No unchecked input.** Dispatch needs a single-use permit naming the evidence it relies on, expiring at a deadline derived from that evidence's capture time.
2. **Abort is isolated** from capture, vision, storage, providers and UI. A backend that fails the release gate gets a labelled residual limitation and escalation, never a universal promise.
3. **Uncertainty stays visible.** `unknown` is never `false`; `OutcomeUnknown` never causes automatic retry or replay; recorded coordinates are evidence, never fallback authorization.
4. **Durable means ordered** file-and-directory syncs on a filesystem the app has probed.
5. **User-owned choices stay open**: helper language, compositor, output modes, numbers, retention, license, provider priority.

## 2. Architecture

### 2.1 Processes

| Process | Owns | Never |
| --- | --- | --- |
| Electron main | Lifecycle, typed IPC, project I/O, settings, provider spawning, direct in-app abort route to the guard | Recognition, dispatch decisions |
| Renderer (D-UI, Tailwind) | Approved Actions/Flow/Values, Runs, Settings | Node, network, filesystem, any route to the guard; strict CSP, narrow preload |
| Engine ([utilityProcess](https://www.electronjs.org/docs/latest/api/utility-process)) | Validation, interpretation, policies, run journal, permit issuance | Dispatch |
| `r2a-guard` (native, D-HOST) | Input backend, bounded permit queue, abort sources, held-input ledger, engine watchdog | Capture, vision, disk I/O, providers |
| `r2a-capture` (native) | Frames, passive observation, window/focus queries, clock calibration | Dispatch |
| `r2a-vision` (native) | Matching, OCR, crops, hashes | Dispatch, network |
| `r2a-broker` (optional, D-PRIV) | Authorized Wayland device reads, only if G-W2 needs it | Grabs, commands |

Bun runs workspaces, scripts and pure-package tests; Electron's Node runs the app. **D-HOST** candidates are Rust and C++ (C for the tiny guard is also possible); G-H0 (§9.3) decides from binding probes, packaging/license inventory and crash isolation. v1's Rust-maturity and "no Node bindings" claims are withdrawn as unverified. In-process addons are rejected for the guard because a crash would take Electron down; they also carry a rebuild cost, since native modules "need to be recompiled for Electron" [doc: [native modules](https://www.electronjs.org/docs/latest/tutorial/using-native-node-modules)].

### 2.2 Dispatch and abort isolation

The guard has its own event loop; abort sources are serviced on a thread that blocks on nothing else. The permit queue is bounded (*experimental* depth 1 per lease) and holds only unexpired permits. No pixels cross the guard pipe.

Abort sources: the in-app control (main → guard, bypassing the engine), the backend's global route (§9), optional physical-input detection, engine heartbeat loss/EOF, SIGTERM. Abort increments the lease epoch, drops queued permits, releases every ledger input, then reports results. Pause uses the same release; resume re-observes.

| Failure | Handling | Status |
| --- | --- | --- |
| Engine death/hang | Watchdog releases ledger inputs | [gate] G-X5/G-W5 |
| Capture, vision, fsync, UI or provider stall mid-drag/hold | Abort still releases within the measured bound | [gate] stall matrix |
| Guard SIGKILL/crash | A killed guard cannot clean up. Candidates: (1) backend releases on client/session disconnect; (2) a supervisor reconnects and releases only ledger entries mirrored before each press, after proving it can distinguish guard-owned from physically held inputs (e.g. X11 device/source identity) | [gate] Both unproven. If neither passes on a backend, label "inputs may remain held after helper crash" and escalate (D-WAY / release scope) |
| Compositor or X server failure | Not recoverable; documented | Limitation |

### 2.3 Core contracts (abridged)

```ts
type Observation = { id; sourceId; sourceEpoch; captureMonoNs; uncertaintyNs; clockModelId;
  frameHash; descriptor: SourceDescriptor; recipient?: RecipientEvidence };
type LocateResult =
  | { kind: 'found'; observationId; candidates; method; score; calibrationId }
  | { kind: 'absent' | 'ambiguous'; observationId; detail }
  | { kind: 'unavailable'; reason };                      // never coerced to absent/false
type Relied = { observationId; role: 'locate' | 'check' | 'semantic' | 'confirm';
  maxAgeNs; contextRegions?: RectRel[] };
type DispatchPermit = { permitId; leaseEpoch; runId; revisionHash; actionInstanceId;
  relied: Relied[]; deadlineMonoNs; sourceEpoch; geometryHash; recipient;
  input: PointerCmd | KeyCmd | TextCmd };
type DispatchResult = { permitId } & ({ kind: 'rejected'; reason }
  | { kind: 'acknowledged'; backendSeq } | { kind: 'outcome-unknown'; reason });
type KeyEvent = { platform: 'x11' | 'evdev'; code; edge: 'down' | 'up' | 'repeat'; mods;
  layoutId?; keysymAtCapture?; tMonoNs; recipient?; caps: KeyCaptureCaps };
type Output = { outputId; producerStepId; type; label };  type ValueRef = { outputId; expectedType };
type RecoveryPolicy = { block; exit: 'retry-step' | 'continue' | 'stop'; maxPerOccurrence; maxPerRun };
```

`acknowledged` means the backend accepted the call, not that the app reacted; post-checks verify. Messages are versioned JSON Schema, generated for TS and native; unknown versions and oversized payloads are rejected.

### 2.4 Freshness, refresh, late responses

`captureMonoNs` is the frame's presentation time mapped to `CLOCK_MONOTONIC` via the source clock model; `uncertaintyNs` covers calibration error plus frame interval. Suspend/resume, X timestamp wrap (32-bit ms) or stream renegotiation start a new source epoch; earlier-epoch observations cannot authorize input.

**Deadline** = min over `relied` of `captureMonoNs − uncertaintyNs + maxAgeNs` (per-role values from D-NUM). The oldest evidence still relied upon controls; arrival time and write completion play no part.

**Refresh** is the only way to extend it. The new confirm observation must be journaled (hash, descriptor) before use. It refreshes *locate* evidence when the located region matches within calibrated tolerance under unchanged transforms, and *check/semantic* evidence (branch condition, Jev judgment) only when every declared `contextRegion` is re-verified in the same observation by pixel comparison or re-running the local check. Target pixels never refresh a decision justified elsewhere; undeclared context means re-evaluate or apply the failure policy.

**Late CLI/Jev results** keep their original `observationId`; age runs from that capture, never from arrival.

### 2.5 Persistence critical path

Per dispatch: frame assets are written asynchronously during locate; the engine then group-commits the debug pre-frame (debug mode), the confirm-observation record and the `dispatch.intent` record, syncing each file and, for new files, its directory; then issues the permit. The guard checks epoch, single use, deadline, source epoch, geometry hash and recipient *after* dequeue, immediately before dispatch; expiry returns `rejected: stale` and the engine re-observes. A write exceeding the storage timeout (D-NUM) stops with `storage-failure` and no dispatch. Intent records are written in every mode, so a crash always surfaces `OutcomeUnknown`.

Project save: sync new media then their directories → write and sync the revision, sync `revisions/` → write and sync `project.json.tmp` → rename → sync the project directory [doc: [fsync(2)](https://man7.org/linux/man-pages/man2/fsync.2.html) requires a separate directory fsync]. Revisions store parent IDs, so `history.ndjson` is a rebuildable index; on open, a missing head entry is appended and a truncated last line discarded. A filesystem probe on open/create warns (or refuses, D-STORE) where directory fsync or atomic rename is unreliable. Fault injection at every boundary supports crash-consistency claims only; power-loss claims need separate evidence (VM hard reset).

Run storage failure before dispatch: no dispatch, stop with an in-memory explanation in the UI. After dispatch: release, stop, mark incomplete, never retry. A best-effort log goes to the XDG state directory; logging is **not** guaranteed when all storage fails. Text-only continuation is a D-RETAIN option, not a default.

## 3. Runtime

### 3.1 Lifecycle and attempt

States: `preparing → acquiring-app → running ⇄ paused → completed | stopped(explanation) | aborted | failed-internal`. Preflight freezes and validates the revision and checks required capabilities, a working abort route, assets, output-folder filesystem and debug reserve; any failure refuses the run with a reason.

Attempt: observe → locate (`found|absent|ambiguous|unavailable`) → plan point (§5) → journal confirm observation and verify refreshes (§2.4) → durable intent → permit → guard checks → dispatch → `rejected|acknowledged|outcome-unknown` → post-evidence and explicit post-condition or fixed traced settle. Absent, ambiguous and stale poll until timeout; unavailable is an error (D-NUM may grant short grace). No delay is randomized.

Guard recipient checks: on X11, active window and window-under-point match the window rule [gate G-X4]; on Wayland, per G-W2/W3 outcome. A backend without recipient evidence reports that limitation for user acceptance under D-WAY.

### 3.2 Failure policies

**stop** (default) explains `{code, step, expected, observed evidence, suggestion}`. **pause** releases inputs; resume re-observes. **recover** applies `RecoveryPolicy`: no nesting, drawn in Flow as an annotation rather than a back-edge, so no general cyclic graph. Counters are per `(nodeId, occurrence)` and per run, journaled, restored on resume, never reset by `retry-step`; exhaustion stops with explanation. **`OutcomeUnknown`** stops (or pauses under a pause policy) with a labelled state; no automatic recovery or retry; continuing requires explicit user action after inspection.

### 3.3 App acquisition (WP12)

Attach matches a stored window rule (X11 class/title/process ancestry; Wayland portal picker plus restore token), never a window ID. Launch has separately timed stages `launch-requested → process-seen → window-matched → window-stable → ready-observed` (user-defined screen check). Last Z's launch identifier is verified locally, not guessed; whether it runs as an XWayland client under Wayland is probed, not assumed.

## 4. Recording

**Clocks.** Per-source models (offset, drift, uncertainty, epochs) in `clock.json`. X11 RECORD server timestamps are calibrated by `PropertyNotify` round-trips [gate G-X2]; Wayland follows the G-W2 mechanism. A fixture visual timecode measures skew.

**Frames.** A VP9/AV1 scrubbing video (D-PKG) plus a lossless ring buffer. Each action stores lossless pre-frame (last frame before `press − uncertainty`), press and release frames; template crops come only from these. Unassociable actions record that fact.

**Pointer extraction** (pure TS, raw-event fixtures, D-NUM thresholds): click, explicit double-click grouping, drag path with endpoint targets, hold, aggregated scroll. Sub-frame clicks remain events.

**Keyboard.** Physical key capture is not committed text. Store raw `KeyEvent`s: platform-tagged code, down/up/repeat, modifiers, layout ID, keysym only when the backend reports it, and layout/Compose/IME capability flags (Compose and dead keys are stateful [doc: [xkbcommon compose](https://xkbcommon.org/doc/current/group__compose.html)]). Extraction yields only physical actions (`key`, `chord`, `hold`); gameplay letters stay keys. `text-insert` is explicitly authored: the user types the text in the editor, or confirms a recorder suggestion that shows its keysym interpretation and layout/IME uncertainty, with raw events retained. Physical replay checks the current layout and blocks on mismatch unless the user approves a remap. `text-insert` exists only on backends passing a text gate (G-X3/G-W3); otherwise it shows unsupported.

**Privacy and focus.** Capture happens only during an explicit recording session with a visible indicator. Events reach persistence only with event-time focus/recipient evidence that they targeted the selected app (X11 candidate: `FocusIn/FocusOut` ordering within the RECORD stream [gate G-X2]). Without proven evidence, keyboard recording on that backend stays disabled unless the user decides otherwise (D-KEYCAP). No all-application key logging.

**Feedback isolation.** Recording and running, including re-recording, are mutually exclusive; queues drain between leases; X11 events are also classified by XI2 `sourceid` [gate; [XI2proto](https://xorg.freedesktop.org/archive/current/doc/inputproto/XI2proto.txt)].

**Default target.** A recorded click gets an image target cropped from its lossless pre-frame. Recorded coordinates are evidence only (§5.2).

## 5. Targets and clicks

### 5.1 Geometry

Chain `crop → frame → client → desktop (X11 root / stream-logical) → input`; every link carries a `SourceDescriptor`, and geometry/source changes create a new descriptor and epoch. No hardcoded screenshot offsets. Layers: search area (client-relative, normalized), located box, target-local safe area, exclusions, margin in input pixels.

Sampling enumerates eligible integer input points by scanline intervals, keeps those whose inverse-mapped centres lie in the eroded safe area and client rectangle, and samples uniformly with the traced seed; an empty set stops with `safe-area-empty`. No retry delays or timing change. The variation preview uses the same sampler. Fixed without safe area clicks the exact transformed authored point.

### 5.2 Locator authorization

Each target has **one** selected locator (`fixed|region|ocr|image|jev`). A user-authored fallback chain advances only on `absent` and only where the user marked "try next when absent"; `ambiguous`, `unavailable` and stale go to the failure policy. A fixed link in a chain needs its own authored precondition check. Standalone fixed targets remain, with fresh source, recipient and geometry checks. The trace records the authorizing locator.

## 6. Document, editing, values

### 6.1 Model and validation

Ordered AST: sequence, section, step, wait, if/else-if/else with joins, stop, pause; recovery is a step policy. Inserting a condition splits a recorded section into two over disjoint ranges of the same take (D-SPLIT). Steps, targets and outputs have independent IDs; `ValueRef` binds `outputId` plus expected type; names are labels. The validator checks references, definite availability across arms, joins and recovery-`continue` paths (a failed step's outputs are unavailable on its continue path), types, geometry, recovery non-nesting and online flags. Invalid documents save but cannot run.

### 6.2 Independent copy

Deep clone with a full remap table (nodes, steps, targets, outputs, bindings, recovery blocks). Internal references point inside the copy. External references survive only when definitely available at the destination with matching type; otherwise they are flagged invalid, never rebound by inferred equivalence. Immutable media is shared; executable settings are independent. Test: editing either copy leaves the other byte-identical.

### 6.3 Partial re-record

Select a contiguous range; the first pre-frame is shown as "bring the app to this screen" with optional similarity check. Record a candidate take. The app suggests a step mapping from action kind and target similarity, never timestamp or position. The user confirms the step mapping and, separately, each old output: map to a compatible-type new output (keeps `outputId`), retain the old producer, or leave unresolved (consumers invalid). A type change always gets a new `outputId` with consumers listed. Commit is one atomic revision; undo restores; the old take is retained.

### 6.4 Values and file outputs (D-VALUES, D-FILE)

Recommend run-local typed values (string, number, boolean, image handle). Recommend **create-new only** initially; overwrite/append wait for D-FILE semantics.

| Concern | Rule |
| --- | --- |
| Identity | Attempt key `runId/nodeId/occurrence`, embedded in the filename |
| Write | Stage in destination, sync, publish via `renameat2(RENAME_NOREPLACE)` or `link()`, sync directory, journal `{path, sha256}` [doc: [rename(2)](https://man7.org/linux/man-pages/man2/rename.2.html): not all filesystems support it] |
| Containment | `openat2(RESOLVE_BENEATH \| RESOLVE_NO_SYMLINKS)` from the granted folder [doc: [openat2(2)](https://man7.org/linux/man-pages/man2/openat2.2.html)] |
| Unknown outcome | On retry/restart, look up the attempt key: matching hash = done; orphan staging removed; mismatch stops |
| Unsupported FS | Preflight probe; refuse, or a weaker mode only with user approval |
| Undo | Editor undo never undoes written files; the UI says so |

## 7. Recognition

Candidates: Tesseract for text; normalized template matching with a bounded scale pyramid for icons; non-generative ONNX OCR as alternative. Thresholds and ambiguity margins are method-specific, calibrated from labeled negatives [doc: [OpenCV](https://docs.opencv.org/4.x/d4/dc6/tutorial_py_template_matching.html), [Tesseract](https://tesseract-ocr.github.io/tessdoc/Command-Line-Usage.html)].

**G-R1:** fixture scenes plus Last Z frames captured during gates (loading, popups, menus, resizing, absent and repeated icons; *experimental* ~200 frames). Report hits, misses, wrong-target rate, ambiguity detection, latency by search area. The user approves per-class adequacy (D-OCR/D-NUM). Zero wrong targets in *n* trials supports only an approximate 95% upper bound of 3/*n*; reports state *n* and claim nothing about the population. A failing class offers region/fixed targets or explicitly online Jev, never a silent downgrade.

## 8. Optional AI and Settings

### 8.1 Provider Settings (release-required, WP19)

Per provider: enabled state, executable path/version and tested-profile match, authentication readiness from the CLI's own output, models and capabilities (image, schema), connection test, billing category (CLI subscription, API key, OpenRouter), active invocation profile, managed-policy notice. Steps select providers explicitly; offline workflows never spawn one.

### 8.2 CLI invocation profiles

Base contract: the user's unmodified binary and CLI-owned login; never read or copy tokens; no silent API billing; argument-array spawn in an app-owned scratch directory; capped, schema-validated output; SIGINT → SIGTERM → SIGKILL to the process group; discard late responses. Profiles are version-pinned and each flag probed at the installed version.

| CLI | Candidate profile | Open points |
| --- | --- | --- |
| Claude Code | `-p --output-format json --json-schema … --no-session-persistence --safe-mode --strict-mcp-config` (empty), `--disallowedTools "mcp__*"`, `--tools` limited to scratch-confined Read, or `--input-format stream-json` images | `--safe-mode` disables CLAUDE.md, plugins, hooks and MCP while "Authentication, model selection, built-in tools, and permissions work normally"; "Managed settings policy still applies". `--restricted` only if a probe shows auth survives. `--bare` excluded: no subscription login [doc: [CLI reference](https://code.claude.com/docs/en/cli-reference), [headless](https://code.claude.com/docs/en/headless)] |
| Codex | `exec --json --output-schema … --sandbox read-only --ephemeral --skip-git-repo-check --ignore-user-config --ignore-rules -i` | `--ignore-user-config` skips `config.toml`; "Authentication still uses `CODEX_HOME`". No `exec` flag documented to disable hooks/MCP; project and managed config sources need audit [doc: [Codex commands](https://learn.chatgpt.com/docs/developer-commands?surface=cli)] |

**G-AI1:** sentinel hooks, MCP servers, plugins and instruction files in user and project config stay inactive; user settings hashes unchanged; eligible login works; images use the approved route; cancelled late responses discarded. Managed-policy exceptions are disclosed, never bypassed. If no profile isolates while keeping auth, that adapter is not released for that version and D-AIELIG reopens.

**D-AI-SCOPE** (proposal, not approved): initial CLI scope is authoring assistance (typed edit proposals validated and applied as one undoable revision); CLI runtime judgments later.

### 8.3 Jev runtime adapter (release-required, WP20)

DECISIONS.md requires Jev as an optional runtime method, so the adapter must be complete; using it stays optional. Send only the selected crop with transform metadata [doc: [images](https://docs.typesafe.ai/images)]. Each request bundles presence, uniqueness (instance count), judgment and Box, bound to one `observationId`; Box "has no 'not found' answer" [doc: [Box](https://docs.typesafe.ai/primitives/box)]. Clicks require presence, uniqueness, a plausible box inside the search area, §5 safe-area rules and §2.4 refresh including declared context regions. Provider, malformed or unsupported-model failures yield `unavailable`/`unknown`, never `false` or click permission. The API key lives in Electron `safeStorage`.

### 8.4 OpenRouter and offline enforcement

OpenRouter is optional and outside release closure unless D-OR selects it; a stub is never presented as support. Offline: renderer CSP blocks remote origins and main denies other requests; network/provider imports are allowed only in `packages/providers`; OCR models and fonts are bundled; CI runs end-to-end under `unshare -n`.

## 9. Platform gates

### 9.1 Evidence and stop criteria

Each gate yields a spike, a report with commands and environment fingerprint (distro, session, portal/backend versions, GPU, scale, layout), raw logs, fixture-ledger ground truth and video. [obs] Pop!_OS 22.04/GNOME 42.9/X11 with RECORD, XTEST, XInput, RANDR shows availability only.

Stop criteria are per capability cell: a setup allowance (extension, broker, harness builds) that is not counted as attempts, then a bounded number of documented attempts. Outcomes: `pass`; `pass-with-limitations` (listed); `not-yet-proven` (escalated with evidence and next mechanism); `unsupported` only for a documented API limit or reproduced platform cause, never an arbitrary retry count.

### 9.2 Gate matrix

| Gate | Proves | Acceptance evidence |
| --- | --- | --- |
| G-X1 Pixels | XShm root/window capture, monotonic stamps, fullscreen Proton | Timecode decoded; Last Z frame correct; source loss explicit |
| G-X2 Observation | RECORD events plus event-time focus order; XI2 `sourceid` separation | Ordinary input still reaches fixture and game; coordinates match ledger; non-target keys never persisted; injected events separable |
| G-X3 Dispatch | XTEST click/drag/key/hold/text-insert (keymap remap); non-US, AltGr | Ledger equals commands; user confirms the game reacted on a harmless element |
| G-X4 Transforms, recipient | Move, resize, multi-monitor, scaling; active and under-point checks | Commanded = received after rounding; focus theft → `rejected` |
| G-X5 Abort | XGrabKey over fullscreen game; stall matrix; engine kill; guard SIGKILL both candidates | No held input beyond documented residuals |
| G-W0 Environment | Candidate's portal interfaces/versions introspected on the running backend | `busctl` capture. GlobalShortcuts counts only if introspected; xdg-desktop-portal-gnome NEWS first lists "Add global shortcuts portal backend" at 48.rc [doc: [NEWS](https://gitlab.gnome.org/GNOME/xdg-desktop-portal-gnome/-/raw/main/NEWS)], so older GNOME backends need another route |
| G-W1 Pixels | ScreenCast window/monitor streams, restore tokens, prompts per run [doc: [ScreenCast](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html)] | Timecode decoded; prompt count and repeat-session picker behaviour recorded |
| G-W2 Observation | Per event: post-acceleration compositor-logical coordinates at event time, recipient surface, timestamp in a known clock; native Wayland *and* XWayland | Fixture-received coordinates match under fast move-click-move, drags and acceleration; indeterminate events rejected, never interpolated; loss counted |
| G-W3 Dispatch | RemoteDesktop stream-relative pointer, keycode/keysym, `ConnectToEIS` where exposed [doc: [RemoteDesktop](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.RemoteDesktop.html)]; text-insert | Ledger equals commands for native and XWayland clients |
| G-W4 Transforms | Stream-logical → input at mixed scales | As G-X4 |
| G-W5 Abort | Per compositor: introspected GlobalShortcuts, extension keybinding signalling the guard, or broker abort-chord mode; stall matrix; SIGKILL | Abort with game focused/fullscreen and engine stalled; extension-disabled behaviour reported |

**G-W2 candidates:** W-c compositor integration (extension or Mutter-side hook tagging delivered events; whether an extension sees client-delivered events is unproven); W-a XWayland-scoped RECORD/XI2 (partial coverage at most); W-b evdev plus ScreenCast cursor metadata as cross-check only, since cursor data is buffer metadata, not event-time position [doc: [PipeWire buffers](https://docs.pipewire.org/group__spa__buffer.html)]. InputCapture redirects input to its client and is excluded [doc: [InputCapture](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.InputCapture.html)]. If nothing passes for Last Z and Chrome, D-WAY reopens with evidence; release stays blocked because Wayland is required.

**Optional broker (D-PRIV)** ships only with user approval and this contract: named authorization (e.g. systemd service with polkit `allow_active` for the active local seat plus socket peer-credential check); read-only device access, no grabs; streams only during a recording lease or abort-chord run mode; persists only target-directed events (requires G-W2 recipient evidence); visible indicator; no command surface; uninstall removes it. Tests: threat-model note, refusal of inactive/remote sessions, non-target keys absent from `events.jsonl`.

**D-WAY order:** current GNOME on an LTS distribution chosen by G-W0 introspection, then KDE Plasma 6, then COSMIC. No distribution version is asserted.

### 9.3 G-H0 helper comparison (decides D-HOST)

Per candidate language, probe X11 RECORD/XTEST/XShm, PipeWire ScreenCast, portal D-Bus, libei and Tesseract/OpenCV linkage; measure crash isolation, build reproducibility, prebuilt packaging and license inventory. Binding maturity is judged from the probe, not reputation.

## 10. Debug evidence and Runs

Debug mode is a per-workflow setting recorded in each run header. Traces are append-only, hash-chained NDJSON (corruption detection, not authenticity) with app/helper/profile versions, capability report, seed and calibration IDs in the header.

| Event | Debug mode | Basic mode |
| --- | --- | --- |
| Action attempt | Pre-frame durable before dispatch, candidates, point, transforms, confirm observation, intent, result, post-frame | Intent, result, frame hashes |
| Check (true/false/unknown) | Pre-frame, evidence, result, reason | Result, reason |
| Locator poll | Frame (hash-deduplicated), candidates | Count, final result |
| Branch, value, output write (staged/published/reconciled) | Full | Full |
| Recovery, pause/resume, abort, `OutcomeUnknown` | Full with frames | Full |
| Capture or storage failure | Gap record with reason; nothing fabricated | Gap record |
| Provider call | Provider, model, profile, outcome, latency; no credentials | Same |

Rendering: intent without result = "attempted, outcome unknown"; missing blob = "frame not stored" with reason; no end = "incomplete"; broken chain flagged. Runs viewer: run list, chronology rail with actual branch, raw/overlay stage, before/after, values, "open in editor" at the run's revision with a non-head warning. It has **no IPC route to the guard**; a test asserts zero guard messages during replay. D-RETAIN owns retention, budgets, redaction and export.

## 11. Work packages

| ID | Depends on | Deliverable | Acceptance |
| --- | --- | --- | --- |
| WP0 Scaffold | — | Workspaces, provisional spike harness, CSP shell, CI (unit, Xvfb, `unshare -n`) | CI green |
| WP1 Fixture | WP0 | Moving/repeated targets, delayed readiness, popups, counters, decoys, absent target, context-change scene, text field, layout switch, received-event ledger, timecode; native and in Chrome | Ledger round-trip |
| WP2 X11 gates | WP1 | G-X1–X5 | §9 |
| WP3 Wayland gates | WP1, D-WAY candidate; D-PRIV before broker build | G-W0–W5 | §9 or escalation |
| WP4 Recognition gate | WP1 | G-R1 | Metrics plus finite-sample statement |
| WP5 Helper comparison | WP2 first cells | G-H0 → D-HOST | Report |
| WP6 Schema and store | WP0, D-STORE | Schema, migrations, ordered saves, FS probe, GC quarantine | Fault injection at every sync boundary; migration corpus |
| WP7 Guard and capture (X11) | WP2, D-HOST | §2.2–2.5 | Stale, expired, duplicate, focus-stolen, out-of-rect rejected; stall matrix |
| WP8 Geometry | WP0 | Transforms, scanline sampler, preview | Property tests |
| WP9 Vision | WP4, D-OCR | Matching, OCR | Corpus regression |
| WP10 Recording | WP6, WP7 | Clock models, lossless buffer, pointer and key model, focus filtering | Fixture sessions; skew measured; non-target keys absent |
| WP11 Engine | WP6–WP9 | Interpreter, policies, journal critical path, FakeGuard | Golden traces: absent, ambiguous, unavailable, stale, unknown≠false, outcome-unknown, recovery bounds |
| WP12 Acquisition | WP7, WP11 | Attach, launch stages, readiness | Stages distinct in trace |
| **Slice A** | WP10–WP12 | X11 record → reload → run fixed/image targets → stop with debug | Xvfb and real session |
| WP13 Actions view | Slice A, D-UI | Approved Actions UI, locator-chain editor, variation preview, key/text conversion | Walkthrough; study parity |
| WP14 Flow and Values | WP13, D-SPLIT | Conditions, waits, recovery annotations, validator UI | Cross-view selection; rename keeps bindings |
| WP15 Copy, re-record, undo | WP14 | §6.2–6.3 | Independence; output-mapping tests |
| WP16 Outputs | WP14, D-FILE, D-VALUES | §6.4 | Kill at each publish boundary; vfat refusal |
| WP17 Runs, debug coverage | WP11, WP13 | §10 | Disk-full/EIO before and after dispatch; zero guard messages |
| WP18 Guard and capture (Wayland) | WP3 passing, WP7, D-WAY, D-PRIV if needed | Adapter per gate outcome | X11-equivalent suite |
| WP19 Provider Settings, CLI adapters | WP11, WP13, D-AIELIG, D-AI-SCOPE | §8.1–8.2 | Fake-CLI tests (auth, limits, unavailable model, refusal, transport, malformed, cancel); G-AI1; user smoke test with own login |
| WP20 Jev adapter | WP11, WP14, WP19 | §8.3 | Repeated targets, changed context with unchanged box, delay, errors |
| WP21 OpenRouter | WP19, D-OR | Only if selected | Same contract tests |
| **Slice B** | WP13–WP20 (WP21 if selected) | User-chosen Last Z routine plus Chrome regression, with conditions and debug, on X11 and Wayland | User-witnessed runs |
| WP22 Release | Slice B, D-PKG, D-LIC, D-NUM, D-KEYS, D-RETAIN | Packages, `r2a doctor`, notices, docs | Clean-machine matrix; every §12 row passes on both sessions |

**Critical path:** WP1 → WP3 (G-W2) ∥ WP2 → WP5 → WP7 → WP10/WP11 → Slice A → WP13 → WP14 → WP15/WP17/WP19/WP20 → Slice B → WP22. WP3 starts immediately because it can change observation, recipient and key models.

## 12. Requirement-to-test closure (release gate)

| Confirmed requirement | Packages | Release evidence |
| --- | --- | --- |
| X11 and Wayland | WP2, WP3, WP7, WP18 | Full suite on both sessions |
| Last Z, Chrome, no game-specific core | WP1, WP12, Slice B | Chosen routine plus Chrome regression; no Last Z constants in core (lint) |
| Attach, launch, visual readiness | WP12 | Stage-separated traces |
| Automatic recording | WP10 | Ledger parity |
| Edit, duplicate, partial re-record | WP13–WP15 | Independence and mapping tests |
| Screen branches in one workflow | WP14 | Both arms; false ≠ unknown |
| Moved targets without old coordinates | WP9, §5.2 | Moved-target fixture; vanished target → zero dispatch |
| Text, screenshots, values, files | WP16 | Mode and reconciliation tests |
| Offline core | WP0, WP11 | `unshare -n` end-to-end |
| Own-login CLIs configured in UI | WP19 | G-AI1 plus user smoke test |
| Jev runtime method | WP20 | Contract tests |
| Position-only variation | WP8 | Property tests; no timing variance in traces |
| Stop default, pause, recovery | WP11, WP14 | Golden traces |
| Debug capture, zero-input replay | WP17 | Coverage-table tests |
| Approved spacious UX | WP13, WP14, WP17, WP19 | User study-parity review |

## 13. Decision gates

| ID | Decision | Recommendation | Before |
| --- | --- | --- | --- |
| D-HOST | Native language, process model | G-H0 decides; Rust and C++ viable | WP7 |
| D-WAY | Compositor/version; setup; recipient and helper-kill limitations | Introspected current GNOME LTS first | WP3 (candidate), WP18 |
| D-PRIV | Optional broker | Only if G-W2 needs it; §9.2 contract | Broker build |
| D-KEYCAP | Keyboard recording without proven event-time focus | Disabled | WP10 on that backend |
| D-UI | Renderer framework | React with React Aria | WP13 |
| D-STORE | Project format; unsupported filesystems | File-canonical; warn | WP6 |
| D-OCR | OCR engine, per-class adequacy | G-R1 | WP9 |
| D-NUM | Timeouts, polling, per-role `maxAgeNs`, tolerances, storage timeout, reserve | From measurements | WP22 defaults |
| D-SPLIT | Section splitting | Accept | WP14 |
| D-FILE / D-VALUES | File modes; persistence | Create-new only; run-local | WP16 |
| D-RETAIN | Retention, budget, redaction, export, text-only continuation | Failures plus last N runs; stop on exhaustion | WP22 defaults |
| D-AIELIG | Subscription eligibility | Verify terms; G-AI1 | WP19 |
| D-AI-SCOPE | CLI runtime judgments in first release | Authoring first | WP19 |
| D-OR | OpenRouter priority | After Slice B | WP21 |
| D-KEYS | Shortcuts; invocation during a run | Ignore with notice; abort wins | WP22 |
| D-LIC / D-PKG | License; packages | Apache-2.0; AppImage and .deb | WP22 |

## 14. Residual risks

1. Wayland event-time observation (G-W2) may need compositor integration or the broker, or fail; the user then decides D-WAY.
2. Helper SIGKILL may leave held inputs on some backends; labelled, not hidden.
3. Last Z may reject synthetic input (G-X3/G-W3) or defeat local OCR (G-R1).
4. Conservative refresh may make Jev clicks on animated screens fail often; measured before click capability is approved.
5. CLI isolation may drift across versions; profiles are pinned and Settings flags mismatch.
