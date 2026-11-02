# Record to Automate product and implementation plan

Status: reviewable plan with an approved visual direction. This plan incorporates the GPT-6 Astra high and Claude Opus 5.5 high discussions and the user's subsequent decisions. It authorizes no application implementation by itself. See [the decision record](DECISIONS.md) for confirmed requirements and open choices.

## Product goal

Build an open-source desktop application that turns demonstrated actions into understandable, editable automation. A user records an application, adjusts what each action targets, adds screen checks or branches, and runs the workflow locally. They can replace part of a demonstration without rebuilding the whole workflow and inspect what happened when a run fails.

Linux is first, with X11 and Wayland both required. Last Z running through Steam/Proton is the initial integration target; the architecture must also work with other desktop applications such as Chrome. Windows and macOS will use later platform adapters.

The core works offline. Local OCR and image matching are preferred where they work adequately. Optional Claude Code, Codex, and Jev integrations provide AI assistance or screen-based decisions without making ordinary workflows depend on an AI account.

## Approved experience

Use the selected [Opus v2 design document](design/opus/design-v2.md) and [visual journey](design/opus/journey-v2.html) as the design baseline. The [earlier dark editor study](mockups/editor-visual-studies.html) is historical context. Preserve readable typography, restrained colour, spacious panels, and advanced options revealed when needed. The current action and its screenshot receive priority over technical settings. This follows [GNOME's simplicity guidance](https://developer.gnome.org/hig/principles.html), [Fluent's layout guidance](https://fluent2.microsoft.design/layout), and [Fluent's typography guidance](https://fluent2.microsoft.design/typography).

The product has three connected editing views:

- **Actions:** inspect recorded sections, scrub their source material, edit targets, and re-record selected actions.
- **Flow:** arrange order, readiness checks, and conditional actions. A condition can dismiss a popup or select another action in the same workflow.
- **Values:** inspect where extracted text or screenshots are produced and used.

Add a **Runs** view for the newly requested debug history. It shows the actual path taken during a particular execution, rather than another editable copy of the workflow.

The initial journey is: select a running application or request launch, wait until a configured ready state is observed, record actions, refine their targets, add conditions, test matches, and run. Each stage should explain the next useful action in ordinary language. Invocation can be available in the app and through a configurable shortcut; exact shortcut behavior remains open.

The screenshot is sufficient to design these surfaces. Recognition evaluation will later need representative loading screens, popup states, open menus, changed window sizes, and absent or ambiguous targets. The mockup's Mail routine is an illustration, not an agreed first gameplay routine.

## First release scope

The proposed release includes:

1. Screen recording and automatic capture of supported pointer/keyboard actions, with capture capabilities shown accurately for the active environment.
2. Saved local workflows; manually added steps; editing, independent duplication, and partial re-recording.
3. Fixed positions, user-defined regions, OCR text targets, image/icon targets, search areas, and optional Jev judgments.
4. Explicit waits for readiness, structured conditions, and alternative actions within a workflow.
5. Text extraction, named outputs, screenshots, and explicit file-output actions once their semantics are selected.
6. Bounded click-position variation within a safe target area; no timing randomization.
7. Stop-and-explain failure behavior by default, plus pause-for-help and configured recovery alternatives.
8. Per-workflow debug capture and evidence replay.
9. UI configuration for the user's installed Claude Code and Codex CLIs and optional Jev access.
10. Open-source packaging, installation guidance, and an explicit tested Linux support matrix.

Linked copies are excluded from the initial copying model by user choice. Workflow calls, general loops, schedules, cloud collaboration, browser-specific DOM automation, local AI, and additional provider routing require separate scope decisions. An internal bounded poll for a readiness check is not a general workflow loop feature.

## Recommended architecture

### Process and module boundaries

Retain the preferred Bun, Electron, Node.js, TypeScript, and Tailwind CSS stack. Electron's main process runs in a Node.js environment; the recommended Bun role is dependency management and development/build tooling, not replacing Electron's embedded runtime. The renderer framework and exact versions remain open. [Electron process model](https://www.electronjs.org/docs/latest/tutorial/process-model)

| Component | Responsibility |
| --- | --- |
| Electron renderer | Approved editor, recording review, flow editing, values, provider settings, run inspection |
| Main process and narrow preload interface | Window lifecycle, local project access, app integration, validated communication with services |
| TypeScript workflow engine | Validation, execution order, conditions, value references, timeouts, recovery, cancellation, trace events |
| Platform adapters | Separate capabilities for screen capture, input observation, input dispatch, window targeting, and shortcuts |
| Recognition service | Local OCR/image matching and optional remote judgments; results refer to a particular observation and coordinate space |
| Recording service | Source video, input events, timestamps, coordinate metadata, target crops, and replacement takes |
| Run evidence service | Debug frames, observations, matches, selected click points, outcomes, and read-only replay |
| AI adapters | Supervised CLI sessions, bounded judgments, model capability checks, cancellation, and surfaced provider errors |

Use a worker, utility process, native binding, or local helper for expensive or platform-specific work as established by the feasibility tests. The plan does not select a helper language or third-party input package prematurely. Keep the UI responsive and cancellation independent of an AI process.

### One executable document

Actions, flow, and values edit one versioned document. Do not implement a one-way export that creates an unrelated graph and loses the connection to recorded actions.

The model needs stable identities for workflows, executable nodes, recorded sections, steps, targets, outputs, and source events. Names are labels; a rename must not break a value reference. A copied section receives new executable identities and remapped internal references. Its executable settings are independent even if it references the same immutable source media.

Keep three distinct records:

- **Source evidence:** what the user demonstrated, with historical timestamps and coordinate metadata.
- **Executable intent:** what the edited workflow should do, its branches, targets, waits, and outputs.
- **Run evidence:** what a specific revision actually observed and executed.

Source time does not determine execution order or duration. Trimming reference footage must not silently remove actions. A manually authored action may have no source frame. A timeline can show a selected linear section or executed path; it cannot pretend that one recording demonstrates every possible branch.

The recommended first control-flow model is ordered sections with structured if/else decisions and joins. General cycles and workflow calls remain open. Validate missing targets, broken value references, unsupported structures, and outputs that are available only on one branch. Persistent/global values and file-write rules require explicit choices.

### Partial re-recording

Select actions within a section, record a candidate replacement take, then review old and new actions before acceptance. Show changed targets and any outputs that later steps still need. Do not map actions solely by timestamp or list position. Preserve unaffected branches, original evidence, and undo.

Deleting or replacing an output producer must expose affected consumers. The user can retain an old extraction step, bind an appropriate new output, or leave an explicit invalid reference while editing. Invalid executable bindings prevent starting the affected workflow. Independent duplicates must remain unchanged by a replacement elsewhere.

## Targets and click behavior

Each target distinguishes the **search area**, **located target**, **safe click area**, and **chosen click point**. An image detector's box is not proof that every enclosed pixel is clickable. The user can constrain the intended interior area and exclude neighboring controls or badges.

Find the target from a sufficiently fresh observation, apply the target-relative safe region and margins, sample a point locally if position variation is enabled, then validate the final rounded dispatch point. A fixed-position action stays fixed until a safe region is explicitly supplied. Random offsets do not solve target movement.

Track transformations between screenshot pixels, crops, stream coordinates, window/client coordinates, display scaling, and input coordinates. Revalidate after window/source changes. Never hardcode the supplied screenshot's desktop bar or title-bar offsets into the runtime.

Missing, ambiguous, stale, or empty target geometry must not dispatch an unchecked click. The timeout and configured outcome determine what happens next. Variation changes click position only; readiness waits remain explicit conditions. No guarantee of avoiding bot detection is part of the product promise.

For local recognition, evaluate Tesseract for text and OpenCV-style template matching for icons before selecting dependencies. Test stylized text, abbreviated counters, repeated icons, animation, movement, and scaling. Measure incorrect matches as well as misses. Confidence values and thresholds are method-specific, not interchangeable proof of correctness. [Tesseract outputs](https://tesseract-ocr.github.io/tessdoc/Command-Line-Usage.html), [OpenCV template matching](https://docs.opencv.org/4.x/d4/dc6/tutorial_py_template_matching.html)

## Execution, failure, and debug replay

### Execution outcomes

Launching Steam and receiving a successful launch request is distinct from finding the correct application window and observing readiness. Preserve the already-running route. Existing local Steam shortcuts demonstrate the `steam://rungameid/...` convention, but Last Z's exact launch identifier and readiness condition need testing.

A condition that evaluates to false differs from a condition that could not be evaluated. For example, a completed “popup exists” search can return false; capture denial, unreadable text, a timeout, or provider failure cannot silently mean false.

After configured attempts/timeouts, the default is **stop and explain**. Show the failing action/check, expected target, observed evidence, and reason. Alternative step policies are **pause for the user** and **take an explicitly configured recovery path**. Any retry or resumed execution must reacquire the relevant screen state; conversation resumption or an old screenshot is not sufficient.

The engine owns stopping and cleanup, including releasing any held inputs. Provide an always-available abort mechanism validated on each supported platform. Do not depend on the editor or an AI response to stop a run.

### Debug mode

Each workflow can enable debug capture. Capture a frame before every action/check and after each action's completion or verification. Record locator/check attempts as evidence too; identical frames can share stored assets. If capture is unavailable, record that failure instead of fabricating an observation.

For each execution instance, retain:

- Workflow revision, node/step identity, timestamps, attempt, and actual branch taken.
- Original screen evidence, search region, match candidates, OCR text and parsed output, relevant confidence, and ambiguity or failure reason.
- Located target, safe area/exclusions, chosen click point, coordinate transform, and observation freshness at dispatch.
- Requested action, whether dispatch was attempted, completion/verification outcome, produced values, and any recovery action.
- Provider/model and request outcome when AI was involved, with credentials excluded.

Store raw frames separately from overlays so users can switch between the original screen and annotations. Evidence is tied to the revision that ran, so later workflow edits do not rewrite history.

The Runs viewer provides chronological stepping/scrubbing, action and branch selection, before/after comparison, target/click overlays, value inspection, and direct navigation to the corresponding editor step. **Replay displays evidence; it does not click the desktop again.** A new automation run is a separate action.

Basic status/error events exist without full screenshot debug mode. Debug evidence stays local; export is explicit. Storage location, retention, disk budget, capture/redaction behavior, and what happens when evidence cannot be written remain user decisions before release. Failure reporting must still work when screenshot capture or storage fails.

## Optional AI and settings

Expose provider setup in Settings: enabled state, installed CLI path/version, authentication readiness, available models/capabilities, connection test, and applicable usage/billing explanation. Workflow steps select an online provider explicitly. An offline workflow does not spawn an agent or require account setup.

### Claude Code and Codex

Integrate the user's installed, unmodified CLI through documented programmatic interfaces. Let the CLI own its own authentication. Do not copy tokens into the application or offer an undocumented third-party Claude account login.

Codex documents `exec` with structured events/results and reuse of saved CLI authentication. Use a bounded request interface initially; consider its app-server interface if persistent interactive assistance requires it. ChatGPT plan access depends on the user's account, model, and eligible use. A separately authorized Sign in with ChatGPT flow exists for eligible open-source applications, but is not the same integration as invoking a signed-in CLI. [Codex non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode), [authentication](https://learn.chatgpt.com/docs/auth?surface=cli), [open-source plan integration](https://developers.openai.com/cookbook/articles/sign-in-with-chatgpt)

Claude documents programmatic CLI operation and conditions for running its unmodified binary in products with each user's own authentication. Its third-party API/SDK credential rules differ. Validate the exact integration against current provider conditions before advertising subscription support; do not silently switch to API-key billing. In particular, its documented bare mode does not use subscription login. [Claude programmatic interface](https://code.claude.com/docs/en/headless), [integration conditions](https://code.claude.com/docs/en/legal-and-compliance)

Agent assistance can propose workflow edits and configured decisions through typed interfaces. Validate edits before applying them and route executable actions through the local engine so cancellation and debug evidence stay consistent. Surface unsupported models, missing authentication, limits, malformed results, and process failures as explicit states.

### Jev and future providers

Use Jev for optional screen judgments or visual targets. Its current image/Box capabilities are experimental. Box returns coordinates even for an absent target, so combine presence evaluation with localization; do not authorize a click from a box alone. Preserve crop/resize transforms and evaluate actual performance on game frames. These are hosted requests with separate provider access, not automatic benefits of a Claude or Codex subscription. [Jev images](https://docs.typesafe.ai/images), [Box behavior](https://docs.typesafe.ai/primitives/box)

Keep adapter capabilities explicit, including image input and structured results, rather than promising every model supports every action. OpenRouter is the confirmed optional provider for additional models; its release priority and supported capabilities remain open. Local AI remains another optional extension.

## Linux feasibility gates

Screen pixels, observing the user's input, dispatching input, targeting a window, and invoking a shortcut are separate capabilities. Advertise tested combinations, not generic “Linux support” inferred from a screen recording demo.

Observed on the current machine: Pop!_OS 22.04, GNOME 42.9, X11; X RECORD, XTEST, XInputExtension and RANDR are present. FFmpeg, Tesseract and xdotool are installed. This establishes available components, not working synchronized recording or acceptance of injected input by Last Z.

For X11, evaluate RECORD for input observation and XTEST for dispatch, alongside screen capture. Keep recording and playback separated to avoid recording synthetic playback as new user input. Prove synchronization, short clicks, dragging, keys, window motion/scaling, and reliable abort before game testing. [X RECORD](https://www.x.org/archive/X11R7.5/doc/Xext/record.pdf), [XTEST](https://www.x.org/releases/current/doc/xextproto/xtest.pdf)

For Wayland, select an explicit compositor/version and test capture, ordinary click observation, dispatch, and shortcuts separately. ScreenCast supplies PipeWire streams; RemoteDesktop supplies authorized control. InputCapture redirects input when activated and does not establish a transparent recorder of ordinary gameplay clicks. Current portal documentation does not prove support in the installed desktop/backend. [ScreenCast](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.ScreenCast.html), [RemoteDesktop](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.RemoteDesktop.html), [InputCapture](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.InputCapture.html)

If ordinary Wayland input observation cannot meet the requirements, present tested options and setup consequences for user selection: compositor integration, an appropriate local helper, or a local interactive-preview approach. Do not silently substitute a different interaction model or drop Wayland support. Do not change the current desktop/session as part of planning.

## Delivery sequence and acceptance

No calendar estimates are committed before the platform spikes. Each phase ends with working evidence and a review of remaining choices.

| Phase | Deliverable | Acceptance evidence |
| --- | --- | --- |
| 0. Platform and recognition proof | X11 and chosen Wayland capability reports; synchronized capture/observation prototype; separate dispatch/abort proof; recognition fixture evaluation; CLI subscription smoke tests | Ordinary input still reaches the target; captured positions map to frames; dispatch and abort work in a harmless fixture; limitations and permissions explicit; no claim of untested support |
| 1. Offline vertical slice | Local project model, record/edit/save/load/run, target window selection, fixed/image target, startup wait, initial run events and debug frames | A short demonstrated fixture workflow survives restart; failed readiness stops with evidence; core works without network; a minimal Last Z routine is selected for integration testing |
| 2. Approved editor and conditions | Actions/Flow/Values surfaces, OCR outputs, structured if/else, independent copies, partial re-record and undo, safe-area position variation | Cross-view selection agrees; rename preserves bindings; replacement exposes broken consumers; false differs from failure; all sampled clicks remain in valid safe geometry |
| 3. Debug and recovery | Runs viewer, screenshot/overlay replay, before/after inspection, timeout explanations, pause and configured recovery alternatives | A failed run can be understood from its saved evidence; replay sends no input; evidence survives workflow edits; abort and evidence-write failure behave predictably |
| 4. Optional AI | Settings for Claude/Codex, validated assistance, Jev screen-decision adapter, provider error/cancellation handling | Each user connects their own supported account; online use is explicit; offline workflows remain usable; missing/uncertain targets and AI failures do not cause unchecked clicks |
| 5. Open-source Linux release | Tested X11/Wayland support matrix, packaging, dependency/license review, setup guidance, migration/retention controls | Clean-machine installation, local asset availability, correct capability reporting, end-to-end fixture and chosen game routine tests, readable/debuggable failures |
| Later | Windows/macOS adapters; separately chosen provider/control-flow extensions | Shared engine contract plus platform-specific end-to-end evidence |

## Verification strategy

Use real behavior tests where correctness matters: document validation and migrations; branch/value availability; independent copies; replacement/undo; matching misses and ambiguity; coordinate transforms; click geometry after scaling/rounding; timeout/recovery/cancellation; and debug evidence fidelity.

Integration tests use a harmless local fixture with movable targets, delayed readiness, repeated icons, popup branches, readable counters, and intentionally missing targets. Add real game/application observations after that baseline. A single screenshot cannot prove recognition accuracy or dynamic behavior.

End-to-end acceptance covers record → edit → save/reload → execute → fail/recover → inspect/replay, with and without AI. Test the packaged application on each advertised session/backend. Choose performance and storage budgets after measuring representative recordings and debug runs rather than inventing guarantees now.

## Remaining decisions

Before selecting implementation dependencies: renderer framework, native adapter strategy, project storage/container, first Wayland test environment, and recording-to-condition editing boundaries.

Before release defaults: timeouts/polling/recognition thresholds, value persistence and file-write behavior, debug retention/budget/redaction, shortcut behavior, open-source license, packaging/update policy, and optional provider scope.

The approved visual direction, independent-copy rule, stop-by-default policy, local-first execution, and position-only click variation do not need to be re-decided to resolve those details.
