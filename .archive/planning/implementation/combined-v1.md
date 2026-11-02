# Combined implementation plan v1

Status: synthesized from the independently authored and mutually reviewed [Astra v2](astra/plan-v2.md) and [Opus v2](opus/plan-v2.md). Awaiting fresh review by both models. No application implementation or live platform/provider tests have been performed.

[Confirmed decisions](../DECISIONS.md) govern. The [approved visual study](../mockups/editor-visual-studies.html) governs appearance and editor organization. Technical recommendations below remain proposals; [merge decisions](MERGE-DECISIONS.md) explain how the authors' differences were resolved.

## 1. Scope and delivery rule

Deliver an easy-to-use, open-source desktop application for recording, editing, running and debugging workflows. Preserve Bun, Electron, Node.js, TypeScript and Tailwind CSS. Linux X11 and a specifically tested Wayland environment are both required; Windows/macOS follow later. Last Z through Steam/Proton is the first integration target, with Chrome regression coverage and no game-specific constants in the engine.

The first release includes automatic screen/action recording; fixed, region, OCR and image targets; screen-state conditions; independent duplication; partial re-recording and undo; text/screenshot/value/file outputs; position-only safe-area variation; stop-and-explain by default with pause and configured recovery alternatives; debug evidence replay; UI-configured Claude Code/Codex CLI integrations and optional-at-use Jev runtime judgments.

OpenRouter is an optional additional adapter whose release priority remains user-owned. No linked copies, general workflow loops, subworkflow calls, schedules, arbitrary scripts or automatic locator-fallback chains are added. Bounded readiness polling and explicitly bounded recovery are engine policies, not general graph cycles. No timing randomization or bot-detection evasion guarantee is included.

An X11-only build can be a development preview. It cannot satisfy the required dual-session release. A failed required capability returns to the user with evidence and alternatives, never an implicit scope reduction.

## 2. Decisions and early evidence

| Gate | Recommendation or required choice | Blocks |
| --- | --- | --- |
| D-UI | Recommend React + Vite with TypeScript/Tailwind; approve framework and versions. Use the approved visual tokens and progressive disclosure. | Production renderer |
| D-NATIVE | Select C++ or Rust after concrete binding/build/package probes. Keep the same out-of-process contracts either way; no mixed-language expansion without a reason. | Production native services |
| D-PLATFORM | Select exact Wayland compositor/backend/version and acceptable setup. Approve any extension, broker or deeper compositor integration separately. | Wayland setup and support claim |
| D-STORE | Recommend directory-canonical projects and supported local filesystems; approve format, filesystem scope and import/export behavior. | Durable project store |
| D-SEMANTICS | Approve linear section splitting at conditions; run-local typed values; recommend create-new file outputs initially. Persistent/global values and other file modes remain open. | Relevant model/editor/output work |
| D-EXEC | Approve measured timeout, polling, freshness, recognition, input-hold and recovery limits; exact shortcuts and active-run behavior. | Default profiles and execution qualification |
| D-EVIDENCE | Approve debug retention, disk budget, minimum non-debug evidence retention, redaction, export, and storage-failure behavior. Recommend stop on insufficient durable evidence. | Shipping evidence defaults |
| D-AI | Verify exact CLI profiles and account/use eligibility. Recommend initial CLI editing assistance; runtime CLI judgments need an explicit scope decision. Runtime Jev remains required as an optional method. | Declared provider support |
| D-ROUTER | Decide whether OpenRouter belongs in the initial release and which capabilities/models are supported. | Conditional router adapter |
| D-RELEASE | Choose open-source license, package formats and update policy; Apache-2.0 and a Debian package are recommendations, not decisions. | Public release |

Spikes use explicitly authorized provisional tooling and do not require choosing the production renderer or native language first. For each capability, agree a setup allowance, investigation budget, matrix, sample count and experimental pass criteria before testing. Outcomes are passed, failed with a reproducible cause, or inconclusive with next options. Budget exhaustion alone does not prove platform impossibility. Numeric experiment limits are not shipping defaults; zero errors in a finite corpus are not an accuracy guarantee.

Build a harmless fixture first: native X11/Wayland and browser scenes with an input-receipt ledger, visual clock, movable/repeated/absent targets, popup branches, delayed readiness, text/layout cases and controlled animation. Its received-event ledger provides ground truth for input and coordinate tests.

## 3. Architecture and ownership

| Component | Owns |
| --- | --- |
| Electron renderer | Approved Actions/Flow/Values, new Runs, setup and Settings; no direct filesystem/network/native-input access |
| Main and preload | Lifecycle, operation-specific validated IPC, settings/project access, supervised services, a direct in-app abort channel bypassing the engine |
| TypeScript engine utility process | Document validation, interpretation, values, policies, evidence dependencies, permit issuance and run events |
| Native input guard | Dispatch, bounded revocable queue, desktop lease, input ownership ledger, independent abort route and engine watchdog; no capture, vision, disk or provider work |
| Capture/observation service | Screen frames, ordinary input observation, authoritative source/recipient metadata and clock calibration |
| Vision service | Local OCR, matching, crops and candidate results; no dispatch or network |
| Project/evidence services | Immutable commits, assets, run journals, migrations, replay readers, retention and export |
| Provider adapters | Versioned CLI/HTTP contracts, capability checks, explicit data selection, bounded requests, typed failures and cancellation; no desktop authority |
| Optional approved broker/bridge | Narrow active-session device/recipient integration only if a passing Wayland technique requires it |

Proposed layout: `apps/desktop/{main,preload,renderer}`, `packages/{schema,engine,geometry,recording,store,evidence,host-protocol,providers}`, `native/{guard,capture,vision}`, `integrations/<approved-backend>`, `fixtures/target-app`, and contract/scenario/package tests. These are future deliverables, not directories to implement during planning.

Bun manages workspaces/scripts and appropriate pure-package tests; Electron's embedded Node runtime runs the application. Keep the renderer sandboxed, context-isolated, with a strict CSP and a narrow preload API. Validate protocol versions, request/occurrence IDs, payload bounds and asset handles. Slow work cannot block the guard's abort scheduling. [Electron process model](https://www.electronjs.org/docs/latest/tutorial/process-model), [security guidance](https://www.electronjs.org/docs/latest/tutorial/security)

All process boundaries remain valid whether D-NATIVE selects C++ or Rust. Compare actual X11/PipeWire/portal/recognition bindings, dependency licenses, reproducible builds and clean-machine packaging. Do not decide from unverified maturity or ABI generalizations.

## 4. Platform and recording qualification

The current machine has Pop!_OS 22.04/GNOME 42.9/X11 with RECORD, XTEST, XInputExtension and RANDR advertised. FFmpeg, Tesseract and xdotool are installed. These are availability observations only.

| Cell | X11 candidate | Wayland candidate and required evidence |
| --- | --- | --- |
| Pixels | XShm/XComposite or measured capture pipeline | ScreenCast/PipeWire; actual capture interval/clock, correct target, resize/fullscreen, source loss, denial and repeated-session prompts |
| Passive input | RECORD plus XI2 where demonstrated | Compositor delivery hook carrying accelerated coordinates, recipient and timestamp; deeper compositor integration only by approval. XWayland RECORD/XI2 is a partial-client candidate, not native-Wayland coverage. |
| Event/frame association | Calibrated server timestamps and monotonic clock | Prove delivery-time coordinates and recipient against fixture receipts under fast move-click-move, sub-frame clicks, acceleration, drag and focus changes. Cursor-per-video-frame metadata is corroboration only. |
| Dispatch/text | XTEST and separately evaluated text methods | Authorized RemoteDesktop/EIS where exposed; test native Wayland and XWayland separately. Probe physical keys, layout/AltGr/Compose/IME/Unicode capabilities. |
| Geometry/recipient | Root/client/window/focus mapping | Stream/client/compositor/input mapping; mixed scale, rotation, movement, hotplug, focus theft and target replacement |
| Shortcuts/abort | Tested global key grab | Introspected GlobalShortcuts, compositor keybinding or approved abort-chord broker mode; out-of-focus/fullscreen operation independent of UI/engine |
| Fault release | Guard watchdog and backend-specific ownership | Engine, capture, vision, storage and provider stalls; guard SIGKILL; disconnect/reconnect; overlapping physical-user holds |

Portal specifications do not establish installed support. InputCapture diverts activated input rather than proving transparent observation of normal gameplay. Record actual interface/backend versions; do not infer capability from a GNOME release label. [ScreenCast](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html), [RemoteDesktop](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.RemoteDesktop.html), [InputCapture](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.InputCapture.html), [X RECORD](https://www.x.org/archive/X11R7.5/doc/Xext/record.pdf), [XTEST](https://www.x.org/releases/current/doc/xextproto/xtest.pdf)

If raw device reads are needed, propose an explicitly authorized system service with active-local-seat and IPC peer checks, an allowlisted device set, no grabs/commands/general dispatch, and expiring target-bound leases. Correlate event-time recipient evidence in bounded volatile buffers; discard non-target/indeterminate events before forwarding or persistence. Lock/logout/session change/permission or bridge loss closes access and clears buffers. Run mode may retain only abort-chord state and emit abort, not a general key stream. Setup must explain indicators, installation, denial, revocation and uninstall. No permanent input-group membership is presumed acceptable.

No all-application key logging is a fallback. A backend without proven event-time keyboard recipient evidence remains unqualified for that requirement; disabling or weakening the requirement requires a user decision, not silent release acceptance. Recipient checks for dispatch must cover the actual input destination, including expected modal/transient surfaces, not simply a sampled top-level title.

Capture retains original server/device/media timestamps, a calibrated monotonic mapping with uncertainty, and source epochs. Suspend, discontinuity and source changes invalidate relevant observations. Lossless ring-buffer frames supply conservative pre/press/release evidence and templates; compressed scrubbing video is reference only. Sub-frame events remain events even if no frame intervenes.

Extract editable pointer clicks/double-click groups/drags/scroll/holds and physical-key transitions. Preserve platform/code namespace, layout/keymap, modifiers, repeats and raw evidence. Physical keys are not committed text. Text insertion is explicitly authored or confirmed with interpretation/IME uncertainty visible and a proven backend method. Layout mismatch blocks physical replay pending explicit mapping. Recording and execution are mutually exclusive; drain queues between leases. Source-device tagging is supplementary, not a universal synthetic-event detector.

## 5. Workflow model and edits

One versioned executable document backs Actions, Flow and Values. Keep immutable source takes, executable revisions and actual run evidence distinct. No lossy one-way recording-to-graph export.

Use a structured tree of sequences, linear recorded sections, actions, waits, if/else and joins. Recovery is a bounded step policy, not arbitrary graph back-edges. D-SEMANTICS controls section splitting at an inserted condition; historical footage is not rewritten. Source time locates evidence, not execution order/duration. Manual actions can lack source frames.

Steps, targets, output ports and source events have separate stable IDs. Values bind producer/output identity and expected type; names are labels. Validate references, types, geometry, definite availability across branches/joins and recovery-continue paths. A failed step's outputs are unavailable on a continue path. Invalid drafts save but cannot run.

Independent copying deep-clones executable settings and IDs, remaps internal references, and retains external producers only when valid at the destination; it never silently rewires external consumers. Immutable media can be shared. Editing either copy must leave the other's executable document unchanged.

Re-record a selected linear range into a candidate take. Show starting-screen guidance, advisory similarity, old/new actions and affected consumers. Suggested mappings never rely on timestamps/list position as identity. Confirm step, target and output identities separately, including meaning and type; same-type output is not automatically the same value. New or incompatible entities get new IDs. Retain/rebind missing outputs explicitly or leave a non-runnable draft. Commit replacement and repairs atomically; undo restores the prior revision without reversing external effects.

## 6. Evidence-bound execution

### 6.1 Outcomes and evidence lifetime

Locate results are `Found`, `Absent`, `Ambiguous` or `Unavailable`, with method-specific candidates/scores. Checks return true, false or unknown/error. Completed absence can mean false for an existence check; capture/OCR/provider failure cannot.

An `Observation` contains source/epoch, a conservative capture-time interval mapped to monotonic time, clock uncertainty, frame hash, transforms and recipient evidence. Arrival, encode and fsync times are not capture time. A permit identifies the frozen workflow revision, run/occurrence/attempt, lease epoch, complete authorization evidence, recipient, operation, geometry and deadline.

For each still-relied-on observation, compute its earliest plausible capture time plus its approved validity budget. The permit deadline is the minimum. Unknown time bounds block authorization. Late AI responses keep original observation identity and age.

Distinguish a historical branch result from an active precondition: selecting a branch is recorded control flow, not an indefinitely valid permission to click. Each subsequent action declares which predicates still justify its side effect. For example, dismissing a popup consumes that action's popup precondition; later menu actions need their own targets/checks, not a continuing assertion that the popup remains visible. A judgment selecting an immediate action stays relevant until that dispatch. Define predicate invalidation/lifetime in schema and tests so neither stale decisions nor permanently expiring historical branches drive execution.

Replace expired local evidence only by re-establishing its relevant predicates on a fresh observation. For semantic/AI evidence, do not infer validity from unchanged target pixels. The conservative initial rule is rerun the complete relevant judgment unless a local check independently proves all required predicates; whether that is useful on animated screens is an early measured gate. Any declared context-region shortcut needs independent qualification before enabling it.

### 6.2 Durable intent, permit and dispatch

1. Observe, locate and evaluate required predicates; stage necessary lossless evidence during processing.
2. Complete file and directory durability barriers for every asset/record needed to justify the side effect and later reconciliation.
3. Durably journal side-effect intent and its complete evidence references in every run mode.
4. Submit a single-use permit. Immediately after dequeue, the guard checks lease/revocation, unused identity, source epoch, recipient, geometry and capture-derived deadline, then dispatches only if valid.
5. Record rejected, attempted/acknowledged, verified or `OutcomeUnknown` separately. Backend acknowledgment does not prove application success. Capture post-action evidence and evaluate explicit verification/settle behavior.

A new confirmation frame that changes authorization must be persisted and linked before use; a hash/metadata record without its required recoverable asset is insufficient. Slow evidence writes or queueing expire the permit and cause bounded re-observation, never timestamp renewal. Measure the complete evidence-to-input critical path before choosing freshness limits. Offline basic mode retains the minimum authorization/reconciliation evidence under D-EVIDENCE; full debug adds inspection coverage.

No automatic fallback from a visual locator to stored coordinates. Each action has one selected locator. Separately authored fixed/region targeting requires fresh source/recipient/geometry. Missing/ambiguous/stale evidence causes no unchecked dispatch and reaches the configured policy after its bounds. No AI process receives a direct desktop-input tool.

### 6.3 Lifecycle, abort and recovery

Lifecycle: prepare/validate/freeze revision → attach or request launch → resolve the target window → observe readiness → execute/check → complete, stop, pause or abort. Steam launch acceptance, process appearance, stable geometry and visual readiness are separate stages with separately explained failures. Confirm Last Z's launch identifier locally; preserve attach-to-running.

One recording or execution lease owns the desktop. The guard services abort independently of capture, disk, vision, provider and UI waits, with a bounded revocable queue and bounded interruptible hold/drag submissions. In-app abort bypasses the engine; a separately qualified global route works with the game focused/fullscreen. Lease expiry, engine death, source/abort-route loss and reconnect revoke pending work. Record already-submitted non-revocable effects honestly.

Release only guard-owned held inputs. A surviving guard's watchdog does not solve guard SIGKILL. Investigate backend disconnect release, isolated synthetic-device ownership or a supervisor/fresh-connection mechanism; none is assumed safe. Test overlap with physically held inputs and uncertain pre-press ledger windows. A failed required release/abort gate blocks qualification; any proposed residual limitation requires explicit user acceptance and a changed support claim.

Default on bounded failure is stop-and-explain with action/check, expected result, observed evidence and reason. Alternatives are pause-for-help and a configured, nonrecursive recovery block with explicit retry/continue/stop exit. Persist recovery budgets per occurrence and run before entry; retry/resume cannot reset them. Resume reacquires readiness and target state. `OutcomeUnknown` prevents further automatic side effects or recovery retries until explicit reconciliation/user resolution; the UI may park for inspection, but must not silently resume.

## 7. Recognition, geometry and click variation

Evaluate local Tesseract and template-matching candidates before selecting production dependencies; a non-generative local OCR alternative can enter the same evaluation if needed. Use calibration and held-out fixture/game/browser cases covering stylized text, abbreviation, scale, repeated icons, absence, animation and changed context. Report wrong targets, misses, ambiguity, latency, sample denominators and limitations. Jev cannot substitute for an unmet offline recognition requirement. [Tesseract](https://tesseract-ocr.github.io/tessdoc/Command-Line-Usage.html), [OpenCV matching](https://docs.opencv.org/4.x/d4/dc6/tutorial_py_template_matching.html)

Separate search region, located box, user-confirmed safe area, exclusions and chosen point. A detector box is not a measured hitbox. Recommend rectangular safe areas/exclusions initially. Compose crop/frame/client/compositor/input transforms; invalidate on source/scale/focus changes. Use the backend's tested dispatch coordinate precision. For pixel-grid backends, build eligible integer points as scanline intervals whose inverse-mapped centres remain safe; do not allocate a full-screen list. Sample locally using a recorded algorithm/seed and validate after final quantization. Empty geometry fails. The editor preview uses the same geometry/sampler. Fixed clicks do not vary unless the user supplies a safe region. Position-only variation never changes timing or substitutes for readiness checks.

## 8. Storage, outputs and debug

Recommend directory-canonical projects: manifest/head, immutable JSON revisions with ancestry, immutable finalized takes, content-addressed media and append-only run journals. A rebuildable index is a cache, not another source of truth. Approve supported filesystem scope under D-STORE.

Save order: stage/fsync/publish assets and sync affected directories; do the same for revision/ancestry; fsync the replacement manifest, publish atomically and sync its parent. New directories need parent ordering too. Head is authoritative; recovery verifies references/checksums, detects interrupted tails, preserves coherent head, quarantines incomplete staging and rebuilds indexes. Garbage collection traverses retained revisions/takes/runs and active staging leases before deleting assets. Migrations preserve backups and create new revisions; future schemas open read-only. Imports enforce path/size constraints; exports materialize references.

Probe primitives on the chosen filesystem and declare tested mount/storage scope. A successful capability probe or process-kill test does not certify power-loss behavior; stronger claims need separate tests. Unsupported destinations are refused with an explanation, not given silent weaker durability. [Linux fsync](https://man7.org/linux/man-pages/man2/fsync.2.html)

Recommend run-local values and create-new text/JSON/PNG outputs initially, gated by D-SEMANTICS. External side-effect identity is `(runId, occurrenceId, outputSlotId)`, assigned before attempts; retry attempts reuse that logical identity. Journal identity, selected destination/path and expected content hash before writing. Stage in destination, fsync, publish without replacement, sync its directory and durably acknowledge. A missing acknowledgment triggers reconciliation by that identity/path/hash; mismatch or uncertainty stops, never writes another copy automatically. Enforce containment against traversal/symlink races. Unsupported filesystem primitives block that output route. Editor undo never removes files already written. Other modes need their own explicit atomicity/retry contracts. [rename semantics](https://man7.org/linux/man-pages/man2/rename.2.html)

Debug is per-workflow and frozen into the run header. Raw frames and overlays are separate; hashes deduplicate assets. The trace pins workflow revision, source links, occurrence/attempt, versions/calibration, actual branches/values, transforms, target candidates, chosen point, observation age, dispatch and verification state. Hash chaining detects corruption, not authenticity.

| Event | Debug coverage | Basic mode |
| --- | --- | --- |
| Action/check/locator attempt, including false/failed checks | Recoverable pre-frame, search/candidates/OCR, predicates and result; post-action evidence after completion/verification | Status plus minimum evidence/intent required by §6 |
| Branch, value, output publication/reconciliation | Actual path/value/effect identity, relevant frames and outcomes | Basic result/reconciliation events |
| Recovery, pause/resume, abort and unknown effects | Chronology, frames where available, counters and reasons | Status/reasons/intent |
| Provider call | Observation/model/profile, selected data, result/error and latency; no credentials | Same essential metadata |
| Capture/storage failure | Explicit gaps and known partial state; no fabricated frame | Best-effort status, not a guarantee of persistent logging |

Pre-dispatch evidence failure prevents the side effect. Post-dispatch failure stops, revokes/releases and exposes incomplete/unknown outcomes; no automatic retry. All-storage failure may defeat emergency logging too; report in memory where possible and detect incomplete traces on restart. No text-only continuation is silently enabled.

Runs provides chronology, actual branch/attempts, before/after, raw/overlay comparison, values and navigation to the historical revision. Its replay component has no dispatch capability; starting a new run is a separate explicit command. Test zero host-input messages during replay. Retention, budgets, redaction and export remain D-EVIDENCE choices.

## 9. Optional AI and Settings

Settings includes enabled state, installed CLI path/version, tested invocation profile, auth readiness from the CLI itself, actual model/capability availability, connection test, online data selection and billing category. Detect mismatch and typed not-installed/auth/model/refusal/limit/transport/malformed/cancel/crash failures. An offline workflow never spawns an agent or makes provider requests; bundle required local assets and test with outbound networking blocked.

Use the user's unmodified Claude Code/Codex binaries and their own eligible login. No token extraction, copied login cache or silent API billing. Version profiles specify arguments, effective user/project/managed configuration, tool/MCP/plugin/hook controls, structured output, image route, output bounds and cancellation. Candidate documented controls must be tested in combination; a profile is unsupported if isolation, image delivery and eligible login cannot coexist. Managed policy is disclosed, never bypassed. Spawn argument arrays in an app-owned scratch directory; no arbitrary desktop tools or uncontrolled file authority. Validate proposed workflow edits into a reviewable undoable revision. [Claude programmatic interface](https://code.claude.com/docs/en/headless), [CLI controls](https://code.claude.com/docs/en/cli-reference), [product conditions](https://code.claude.com/docs/en/legal-and-compliance), [Codex execution](https://learn.chatgpt.com/docs/non-interactive-mode), [configuration](https://learn.chatgpt.com/docs/config-file/config-reference)

The profile report records exact tested versions and options. Probe named Claude safe/restricted/tool/MCP controls and Codex ignore-user-config/rules, sandbox, schema and image controls where supported; do not assume one flag disables every inherited component. Claude bare mode is not used to satisfy subscription-login requirements. Sentinel hooks/plugins/MCP/instruction files, unchanged settings hashes, eligible auth, allowed images and cancelled late responses are acceptance cases. Cancellation invalidates requests/leases immediately and terminates child process groups after bounded grace; late results cannot mutate a newer revision/run.

Jev is an explicit hosted experimental capability. Bind presence, uniqueness, judgment, Box and crop/resize transforms to the same observation. A box alone never permits input; absent, uncertain, malformed or provider-error results remain absent/unknown/unavailable as appropriate. Apply §6 semantic freshness and §7 geometry. Evaluate repeated targets, changed context with unchanged button, delayed responses and animation before enabling click capability. Store API credentials only through a qualified OS-backed secret store; surface an unavailable secure store rather than silently claiming plaintext is encrypted. [Jev images](https://docs.typesafe.ai/images), [Box behavior](https://docs.typesafe.ai/primitives/box)

OpenRouter implements the same capability/error/evidence contracts only if D-ROUTER selects it. No stub counts as supported routing; models with different image/schema capabilities are qualified separately. CLI subscription billing and API-provider billing remain distinct. Local AI is later optional scope.

## 10. Work packages and dependency closure

`G-*` are evidence reports, `W-*` are implementation packages, and `D-*` are user decision gates. Gates apply before the relevant production work; provisional experiments do not decide production dependencies. Default values must be approved before qualifying production behavior, not only after release coding is finished.

| ID | Prerequisites | Deliverable and acceptance |
| --- | --- | --- |
| W00 Baseline and fixture | Spike authorization | Requirement IDs, reproducible fixture, ledger/timecode, provisional harness/CI. Verify delayed/absent/moved/repeated/context-change scenes. |
| W01 X11 proof | W00 | G-X pixels/observation/clock/dispatch/recipient/text/abort/release reports; actual desktop and fixture receipts, not extension availability. |
| W02 Wayland proof | W00; D-PLATFORM for chosen setup | G-W equivalents for native Wayland and XWayland. Setup approval precedes broker/bridge build. Failed/inconclusive required cells block qualification. |
| W03 Recognition and native comparison | W00; first W01 cells; W02 findings as available | G-R corpus plus G-N binding/build/package comparison. Recommendations for D-NATIVE/local OCR with measured limitations. |
| W04 Provider feasibility | W00; permitted test access | G-A exact CLI profiles/login/images/isolation; Jev freshness/usefulness contract evaluation. Offline fake-provider tests first; no copied credentials. |
| W05 Schema/protocol and store | W00; W01–W03 findings; D-STORE/D-SEMANTICS; relevant D-UI | Validated AST/contracts, immutable revisions/assets, migrations/GC and crash ordering. References, joins/recovery, remapping and publication faults tested. |
| W06 X11 guard/capture | W01 passed; W05; D-NATIVE/D-EXEC | Production isolated guard, capture/observation, abort/lease/clock contracts. Stall/kill/physical-overlap, duplicate/stale/source/focus tests. |
| W07 Geometry and vision | W03 adequacy; W05; D-NATIVE/D-EXEC | Locators, transforms, finite-grid sampler and preview. Moved/absent target never falls back to old coordinates; quantized points remain safe. |
| W08 Recorder | W05–W07; D-EVIDENCE capture choices | Video and lossless frames/raw input, extraction, target-scoped persistence. Ledger agreement, short events, layout/IME limits and source changes visible. |
| W09 Engine and acquisition | W05–W07; D-EXEC | Interpreter, policies/counters, durable authorization/intent, attach/verified Steam launch/readiness. Slow-fsync/queue, uncertain result and recovery tests. |
| Slice A | W08–W09 | X11 fixture record → edit → save/reload → run → stop with debug evidence; both fixed and image cases. Development milestone only. |
| W10 Actions/Flow/Values | Slice A; D-UI/D-SEMANTICS | Approved spacious UI, condition insertion, validation, OCR/value references. Cross-view identity and false-vs-error cases. |
| W11 Copy/re-record/undo | W10 | Independent closure remap, candidate take/review, separate output mapping and atomic undo; independent copies unchanged. |
| W12 Outputs | W09–W10; D-SEMANTICS | Text/screenshot/value reuse and selected file modes. Logical-effect reconciliation across attempts, collision/containment/unsupported-FS tests. |
| W13 Runs/debug/recovery | W09–W10; D-EVIDENCE/D-EXEC | Per-workflow toggle, complete coverage table, historical replay, policies, budget/export controls. Disk-full/EIO/gaps/crash, counter persistence and zero-input replay. |
| W14 Wayland production | W02 passed; W05–W09; D-PLATFORM/D-NATIVE/D-EXEC | Qualified adapters/setup/diagnostics and recording/dispatch parity on selected environment. No waived cells hidden as support. |
| W15 CLI Settings and adapters | W04 profile pass; W09–W10; D-AI | Completed Claude/Codex setup and assistance; model/auth/error/isolation/image/cancel tests, actual eligible-user smoke evidence. |
| W16 Jev | W04 findings; W07/W09/W10/W15; D-AI | Completed optional runtime method, presence/uniqueness, complete semantic refresh, ambiguity/provider/error fixtures and qualified live capabilities. |
| W17 OpenRouter, conditional | W15; D-ROUTER selected | Real router adapter/Settings and per-model capability/evidence/error tests; otherwise explicitly later scope. |
| Slice B | W10–W16; W17 if selected | User-selected minimal Last Z routine and Chrome regression on both sessions, conditional actions, outputs, safe variation, recovery and debug inspection. |
| W18 Open-source release | Slice B; all applicable D gates incl D-RELEASE | Clean-machine packages, licensed local assets, diagnostic command, support matrix, docs, migrations/backup/retention and requirement closure below. |

Critical path: fixture → Wayland event-time/abort proof alongside X11 proof → native decision → qualified guard/recorder/engine → complete editor/debug/provider features → dual-session scenario → release. Provider/recognition feasibility runs early in parallel. UI familiarity must not postpone the platform proof until after most application coding.

## 11. Requirement-to-evidence release matrix

| ID | Requirement | Owner and release evidence |
| --- | --- | --- |
| R01 | X11 and Wayland | W01/W02/W06/W14, full suite on each advertised backend; exact compositor/portal/GPU/scaling/layout versions |
| R02 | Last Z and general apps | W00/W09/Slice B, fixture and Chrome plus user-selected game scenario; no game constants in core |
| R03 | Attach/launch/readiness/invocation | W09/W14/W18, verified launch identity, stage-separated timeouts, shortcuts/conflicts/active-run rule |
| R04 | Automatic screen/input recording | W08, fixture ledger/timecode, lossless crops, event-time target filtering, honest key/text capabilities |
| R05 | Edit/independent copy/re-record/undo | W10/W11, identity/output/type/meaning repair and independent document tests |
| R06 | Screen branches in one workflow | W09/W10, both paths, false vs unknown, joins and recovery availability |
| R07 | Fixed/region/OCR/image/moved targets | W07/W10, absent/ambiguous/stale and source-change cases, no implicit fixed fallback |
| R08 | Text/screenshot/value/file outputs | W10/W12, extraction/reuse, selected file modes, logical retry/reconciliation and filesystem boundaries |
| R09 | Offline core | W18, full offline journey with outbound network blocked and bundled fonts/models/assets; no provider spawned |
| R10 | Own-login CLI integration in UI | W15, exact-profile isolation/login/images, Settings, unsupported-model/auth/limit/cancel evidence |
| R11 | Optional runtime Jev | W16, presence/uniqueness, semantic freshness, ambiguity and provider failures |
| R12 | Optional OpenRouter | W17 only if selected; no placeholder marketed as support |
| R13 | Position-only variation | W07/W10, actual quantized point containment/exclusions, traced seed and no timing randomness |
| R14 | Stop default/pause/recovery | W09/W13, bounded failures, durable counters, no automatic unknown-effect retry |
| R15 | Debug screenshot evidence/replay | W13, every coverage row, gaps/failures/history and zero dispatch during replay |
| R16 | Approved easy-to-use dark organization | W10/W13/W15/W18, user review of primary tasks, progressive disclosure, setup/diagnostic clarity |
| R17 | Open-source release | W18, chosen license/notices, clean installation and supported package/update policy |
| R18 | Future Windows/macOS | Shared W05 contracts; later separately tested adapters, no present support claim |

## 12. Verification and review exit

Use property tests for transforms, quantization, safe geometry and reference remaps; scenario tests for branch/value availability, independent copies, confirmed output mappings, unknown effects, durable saves/outputs and migration. Use the receipt-ledger fixture for native behavior; mocks cannot prove desktop input, event timing or abort.

Inject source/focus changes, delayed frames/fsync/queues, stale semantic replies, capture loss, permissions/revocation, process stalls/kills, disk-full/EIO and unsupported filesystems. Measure capture cost/drops, clock skew, recognition errors/latency, evidence-to-dispatch age, abort/release distributions, storage growth and Runs seek cost. Choose budgets from evidence and user priorities, then turn them into regressions.

Open issues at plan level must have a specific contract or named early proof gate, consequence and stop/reconsideration route. Runtime feasibility is not declared solved by plan review. The highest unresolved gates are Wayland event-time recipient/coordinates; ownership-safe abort/release after helper death; useful recognition/freshness with durable evidence; and exact provider isolation/login plus fresh semantic decisions. No required gate is satisfied by a limitation label alone.

This combined-v1 now requires independent Astra and Opus reviews. Their findings will be dispositioned against a revised final plan, with further targeted review if substantive unresolved blocking defects remain.
