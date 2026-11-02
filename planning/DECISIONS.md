# Product decisions

Recorded on 2 November 2026. This is the source of truth for user choices; example controls in a mockup do not override it.

## Confirmed

| Area | Decision |
| --- | --- |
| Product | Easy-to-use desktop software that records actions and lets users edit, execute, and debug them. Ease of use is the highest design priority. |
| Distribution | Open-source release for other users, not only personal use. The license is not selected. |
| Platforms | Linux first, with both X11 and Wayland required. Windows and macOS follow later. |
| First environment | Current machine: Pop!_OS 22.04, GNOME, X11. The first supported Wayland compositor/version still needs to be selected and tested. |
| First target | Last Z through Steam/Proton. Support other desktop applications, including Chrome, without making the core game-specific. |
| Startup | Work with an already-running application; investigate launching through Steam, then wait for screen-based readiness rather than assuming launch completion means readiness. |
| Recording | Capture user actions automatically alongside desktop recording. The user's term “annotation” means an editable automation action or its target. |
| Editing | Save, edit, duplicate, replace, and partially re-record actions or sections. |
| Conditions | Branch based on screen state and execute different actions inside the same workflow. Calling another workflow is an optional idea, not a requirement for the first release. |
| Recognition | Prefer local, non-generative OCR and image matching where suitable. Find moved text/icons instead of relying only on old coordinates. |
| Outputs | Read screen text, take screenshots, reuse extracted values, and support saving results to files. Exact scope and file-write semantics remain open. |
| Connectivity | Core recording, editing, recognition, execution, and debugging work offline. Optional online AI is allowed. Local AI can be considered later. |
| AI harnesses | Reuse the user’s Claude Code and Codex CLI harnesses and their own eligible subscription authentication; configure integrations in the UI. Do not substitute API billing without making the distinction explicit. |
| Runtime AI | Include Jev as an optional screen-decision/visual-target method. Hosted calls are optional and explicit. |
| Additional providers | OpenRouter is the intended optional provider for access to additional models. Release priority remains open. |
| Selected design | [Opus v2 design document](design/opus/design-v2.md) and [sixteen-board journey](design/opus/journey-v2.html) are the chosen design following independent authoring, mutual review and iteration. Astra’s alternative design has been removed at the user’s request. |
| Appearance | Approved polished dark visual direction, stronger typography, generous spacing, and progressive disclosure. |
| Editor organization | Approved action editing with screenshot context, understandable flow organization, and values inspection. |
| Click variation | Position variation inside an intended safe click area only. No timing randomization. No claim that this guarantees avoiding bot detection. |
| Failure default | Stop the run and explain the failure after the configured checks/timeout. Also support pausing for the user and an explicitly configured recovery path as alternatives. |
| Copies | Independent copies only initially. No linked shared-definition editing in the first iteration. |
| Debug mode | Per-workflow debug mode captures screenshots and action/recognition evidence for later review and replay. |

## Technical recommendations for review

- One executable workflow document with linked action/timeline, flow, and values views. Source recordings and run evidence remain separate assets.
- A TypeScript local execution engine behind Electron’s process boundary; optional AI supplies validated judgments or proposed edits rather than becoming the only way to run workflows.
- Linear recorded sections connected by structured conditions. Inserting a condition within a recording would split its executable section; the historical footage remains intact. This editing restriction needs explicit review before implementation.
- Versioned local project storage, immutable source takes, reversible replacement edits, and run evidence tied to the workflow revision that actually ran.
- Tesseract and OpenCV are recognition candidates to evaluate, not selected dependencies or proven solutions for the supplied game screen.

## Choices still open

| Choice | Resolve before |
| --- | --- |
| Renderer framework, exact library versions, native bindings/helper, project container and storage index | Implementation of the relevant module |
| First Wayland compositor/version and acceptable setup/permission requirements | Declaring platform support |
| Readiness polling interval, timeouts, match thresholds and observation freshness | Shipping execution defaults |
| Values local to a run versus persistent/global; file paths, formats, append/overwrite behavior | Implementing persistent outputs |
| Recording/debug retention, disk budgets, redaction and export defaults | Shipping debug capture |
| Exact invocation shortcuts and behavior while another run is active | Completing run controls |
| Loops, workflow calls, schedules, and arbitrary graph connections | Adding those features |
| Open-source license, package formats and release/update policy | Public release |
| Optional OpenRouter release priority and supported capabilities | Planning that provider adapter |

Unresolved choices do not imply approval of the prototype’s sample values. They can be resolved when their implementation phase becomes concrete, without reopening the approved overall design.
