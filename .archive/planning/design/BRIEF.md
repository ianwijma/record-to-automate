# Visual design document brief

Historical authoring brief, requested 2 November 2026. The user subsequently selected Opus v2 and requested removal of Astra’s alternative design. The [selected design index](README.md) now governs navigation. The process below describes the completed exploration, not pending design work.

## Requested process

GPT-6 Astra high and Claude Opus 5.5 high each author an independent visual design document. Each reviews the other's first version. Each then revises their own document, recording how feedback was handled. The primary agent reviews and visually verifies both final documents for the user to compare. Keep the two proposals separate; the user did not request a merged design or a winner chosen on their behalf.

## Source of truth

- [User decisions](../DECISIONS.md).
- [Approved dark visual study](../mockups/editor-visual-studies.html). Preserve the approved polished dark direction, spacious hierarchy, screenshot context, understandable Actions/Flow/Values organization and progressive disclosure. Expand the journey rather than restarting styling discovery.
- [Reference game screenshot](../mockups/assets/last-z-reference.png), 2000 × 1125. It is one real reference state, not evidence that invented subsequent screens or hitboxes exist.
- [Implementation plan](../implementation/IMPLEMENTATION-PLAN.md) supplies product constraints only; technical contracts belong there, not on the design boards.

Ease of use is the highest priority. Prefer familiar plain language, one clear next action, readable text and breathing room. Static mockups should communicate the experience without clicking through a prototype. The previous interactive study remains available, but these new deliverables emphasize visual screen design and short explanatory captions.

## Common journey coverage

Cover the journey below with numbered, captioned mockups. Adjacent small states may share one board; every stage needs an actual visual, not only prose. Provide at least 10 substantive boards and a compact coverage index. Design desktop layouts first; readable in-thread presentation can use focused crops or stacked document composition without implying a mobile application is in scope.

1. First opening and empty library: what the app does, offline core, optional later AI; no mandatory account.
2. Setup and capture permissions: explain screen/input/abort access simply. Show permission denied or unsupported as well as ready; X11/Wayland are tested capabilities, not a universal promise. Host portal dialogs are illustrative external system UI.
3. New recording: choose app/window, attach or optional Steam launch, readiness and capture scope.
4. Recording: minimal HUD with clear recording/paused/stopped states and emergency stop, leaving game controls visible.
5. After recording: helpful first review, readable automatic action list, recording strip and selected screenshot.
6. Edit an action: fixed/image/text method, target versus search region versus safe click area, position-only variation, preview that observes without executing. Show found, ambiguous or missing as distinct labelled states. No invented real confidence result.
7. Flow: a simple screen-state branch inside one workflow, understandable Yes/No versus could-not-check, with advanced detail secondary. Source time is not execution order; graph does not imply recorded video for unrecorded paths.
8. Values and outputs: read text, show producer/use, screenshot/file output. Local/global scopes and file modes are still proposals, not confirmed choices.
9. Independent duplicate and partial re-record: selected range, old/new visual review, affected values/targets, accept/discard and undo.
10. Run preparation and live run: app readiness, debug toggle, visible takeover and always apparent pause/stop; do not hide emergency abort behind a menu. Configuration values/hotkeys shown as examples only.
11. Failure and pause for help: default stop-and-explain, expected versus observed, missing/ambiguous/timeout versus unknown effect; no automatic retry of uncertain actions. User can see the difference between inspection and restarting execution.
12. Runs/debug review: before/after, detected target/safe area/actual click, branch taken and output values. Read-only evidence playback must look distinct from operating the desktop.
13. Settings/AI: own-login Claude Code/Codex CLI selection/readiness/billing category; Jev optional online and experimental; OpenRouter optional release scope. Explicit selected data before online use. No copied credentials, fake connection tests or subscription guarantee. Offline workflow remains available.
14. Return to a saved workflow/library: save state, independent copy, recent runs and next action. Include empty, busy, unavailable and success states throughout, not 14 copies of the same editor.

One illustrative Mail example can connect the journey. The real screenshot proves only its visible scene; label invented follow-up screens, workflow steps and sample values as illustrative. Do not present a new product name, default shortcut, file behavior, library/framework choice or match threshold as user approved.

## Deliverables per author

Write only within your assigned `astra/` or `opus/` directory during authoring:

- `design-v1.md`: concise design rationale, page/view inventory, user journey, visual hierarchy, component/state vocabulary, accessibility, responsive document behavior and user-review questions. Link the actual mockup boards, not hypothetical future visuals. Aim 1800–2600 words; prioritize the pictures.
- `journey-v1.html`: polished static visual design document, with numbered screens, captions and accessible scene anchors. Inline CSS/SVG and local images; no external fonts/assets/network. No app behavior simulation. Simple document navigation is fine; core screens must be visible without a tab click.
- `overview-v1.html`: compact static presentation of 3–4 key scenes for comparison in chat, approximately 1200–1800 CSS px at 728 px width. Link the full visual document. It must be recognizably the same design, not a generic teaser.
- Optional authored SVG assets within your directory; code-native UI mockups are preferable to raster generation for precise labels. Use the real screenshot via absolute path for embedding; keep relative file-open fallback if needed. No need to create fake photographs.

Subsequent review and revision tasks will specify `v2` output paths. Keep v1 immutable for meaningful comparison.

## Visual presentation and checks

For in-thread HTML, html/body/outer wrapper have no background, border, banner or side padding. App/mockup surfaces can own backgrounds and 16px padding. Use theme variables with named dark-study overrides, fluid widths and natural content height; no `vh` or height:100% on the page. Fixed-size diagram regions are allowed. No remote assets. All meaningful text remains readable; do not shrink a complete 1440px desktop to illegible miniature text and call it a 728px design. Prefer focused detail or documentary stacking at narrow widths, while showing the desktop composition at full width. Keep labels at least 12px and normal explanatory text around 14–16px. Use text/icons as well as color for states.

Use `html_preview` for visual QA if available; primary agent will `html_preview` then `html_render` final overviews in the parent thread. Do not `html_render` from children. No local web servers or application installation. Record exactly which views/widths were visually inspected versus structurally checked. A good syntax check is not a visual review.

The primary agent applied frontend-design and ui-ux-pro-max skills. Their generic landing-page funnel, Inter and new palette recommendation do not override approved desktop styling. Relevant guidance: hierarchy, proximity, generous spacing, clear focus/labels, sufficient contrast, SVG icons and no reliance on color alone. Static presentation and the user-approved native font direction take precedence over generic animation/font recommendations.

## Review criteria

Assess the other document against user goals, journey continuity, visual quality, clarity, coverage of awkward states, nontechnical ease of use, screenshot fidelity and remaining choice transparency. Identify concrete board/scene and visual correction; distinguish requirement conflicts from personal preference. Review the actual rendered mockups where possible, not just the author's claims. Keep feedback actionable and focused on design, not native implementation architecture. Do not invent findings to appear thorough.
