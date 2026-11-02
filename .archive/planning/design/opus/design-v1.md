# Record to Automate: visual design document, Opus v1

Status: independent first authoring, 2 November 2026. This document hasn't been cross-reviewed or merged. The [shared brief](../BRIEF.md), [recorded decisions](../../DECISIONS.md) and [approved dark visual study](../../mockups/editor-visual-studies.html) govern it. Nothing here approves a sample value, shortcut, file behaviour or library.

- **Full screenbook:** [journey-v1.html](journey-v1.html). It has 14 numbered boards, each with a caption, the visible mockup and notes. All screens are visible without clicking.
- **Overview for comparison:** [overview-v1.html](overview-v1.html). Three scenes, about 1740 px tall at 728 px wide.

## 1. Design thesis

The approved study already fixed the look: polished dark studio, generous spacing, the screenshot as context, and Actions, Flow and Values tabs with progressive disclosure. This proposal keeps all of that. It adds three recurring ideas so the whole journey reads as one product:

1. **Every step reads like a sentence.** The selected action is summarised as a plain sentence, for example: “Find the Mail button inside the bottom-right corner, then click anywhere in the green area”. Each coloured phrase is styled like its overlay on the screenshot: blue outline for the target, white dashed for the search area, green fill for the safe click area. A non-technical user can check a step by reading it and looking at the picture. They don't need to learn the overlay vocabulary first ([board 06](journey-v1.html#b06)).
2. **Four answers, never a score.** Recognition and checks are always reported as words with an icon: *Found one*, *More than one*, *Not found* and *Couldn't check*. Runs add *Timed out* and *Effect unknown*. No percentages appear, because no recognition accuracy has been measured on this game. Nothing is clicked unless exactly one target was found ([boards 06](journey-v1.html#b06), [07](journey-v1.html#b07), [11](journey-v1.html#b11)).
3. **Control and evidence never look alike.** When the app controls the mouse, a red frame and a run bar with a large Stop appear, and Stop is never in a menu ([board 10](journey-v1.html#b10)). Looking at the past uses a new, single-purpose lavender “evidence” colour, with a striped “Replay” ribbon and film-edge borders, and it has no Pause or Stop because nothing is running ([board 12](journey-v1.html#b12)). The same red/lavender split separates “Run again” from “See what happened” on the failure screen ([board 11](journey-v1.html#b11)).

Each screen has **one clear next action**: one primary button, a “Next:” line or a flagged step.

## 2. Board inventory and journey coverage

| Board | Journey stage(s) | States shown |
| --- | --- | --- |
| [01 First opening](journey-v1.html#b01) | 1 | Welcome in three steps, empty library, offline and no-account note, AI off |
| [02 Setup and permissions](journey-v1.html#b02) | 2 | Checklist with input access turned down, illustrative desktop share dialog, Ready / Needs your OK / Not tested yet |
| [03 New recording](journey-v1.html#b03) | 3 | Window picker using the real frame, attach versus Steam launch, what is and isn't captured, minimised window, waiting for ready |
| [04 Recording](journey-v1.html#b04) | 4 | Recording pill over the real frame in its empty sky band; recording, paused and stopped/saving |
| [05 After recording](journey-v1.html#b05) | 5 | First review list with 2 flagged steps, recording strip, real frame for step 3, processing, nothing-recorded case |
| [06 Edit an action](journey-v1.html#b06) | 6 | Close-up with search area, target, safe area, skipped badge and preview points; Fixed/Picture/Text; Found one / More than one / Not found |
| [07 Flow](journey-v1.html#b07) | 7 | Vertical flow with two checks, Yes / No / Couldn't check lanes, an unrecorded path, check inspector |
| [08 Values and outputs](journey-v1.html#b08) | 8 | Value list, made-by → value → used-by row, illustrative read region, file outputs as a proposal, empty state |
| [09 Copy and re-record](journey-v1.html#b09) | 9 | Independent copy dialog, range on the strip, starting screen, old versus new, value-loss fix choices, Undo toast |
| [10 Run](journey-v1.html#b10) | 10 | Preflight with debug toggle, blocked / game not open / another run active, run bar, takeover frame on the real screenshot |
| [11 Stopped or paused](journey-v1.html#b11) | 11 | Stop and explain with expected versus seen, effect unknown, the reason vocabulary, inspect versus restart, pause for help |
| [12 Runs and replay](journey-v1.html#b12) | 12 | Run list, lavender replay with before/after, found / safe area / actual click, branch, values, version, no-runs and missing-frame wording |
| [13 Settings and AI](journey-v1.html#b13) | 13 | Offline reassurance, Claude Code found and not yet checked, Codex not installed, signed-out error, Jev off, OpenRouter as a proposal, consent sheet |
| [14 Back to the library](journey-v1.html#b14) | 14 | Stopped, independent copy, running, unavailable and empty cards; Saved / Saving / Couldn't save; toast |

All 14 stages have their own visual. The journey header also has a compact table of awkward states.

## 3. Journey highlights

- **01–03 Getting started.** One sentence (“Do it once. Let your computer repeat it.”), three numbered cards and one button. “No account” and “offline” appear twice, so optional AI doesn't read as a requirement. Setup lists three needs in everyday words. A refused permission is red, with words and one way forward. The desktop's dialog is drawn in a light system style labelled illustrative. Sessions show as “Tested setup” or “Not tested yet”, never as a general promise. The recording sheet's three parts are window, start method and what gets captured. Launching from Steam waits for a ready screen and says what it's looking for.
- **04–05 Recording and first review.** A pill sits in the real frame's empty top band, clear of the counters, the button columns and the chat. Its states differ in shape: red dot, amber square, tick. Processing lists what's done. The review is a numbered list of sentences with status chips, under a banner (“8 steps found · 2 need a look”). The greyed Run button shows its reason.
- **06–08 Editing.** The approved editor split is kept. The legend explains each mark in one sentence. The three answer cards use real pixels honestly: Mail found once, four real “EXP” bubbles for *More than one*, and a misplaced search area for *Not found*. Flow is a top-to-bottom list. Each check has Yes / No / Couldn't check lanes, and an unrecorded path is dashed, with no footage. Values use a made by → value → used by row. Outputs carry a pink “Proposal · not decided” chip.
- **09 Copy and re-record.** “The copy is separate” is the dialog's key line. A re-record range on the strip leads to the real starting frame, then an old-versus-new review. Three named choices appear if a value would be lost, followed by an Undo toast.
- **10–12 Running and looking back.** The preflight has a debug toggle. Blocked states explain themselves. During the run, a red run bar with “Stop now” and a red frame around the game are always visible. A stop shows expected versus seen, reason chips and a separate *Effect unknown* card that promises no automatic retry. Next steps are split into lavender “See what happened” and red “Run again”. Pause for help is an amber bar. Replay has its own colour, stripes and film edges, plus before/after, the branch taken, values and the version that ran.
- **13–14 Settings and return.** “Everything works without these” comes first. CLI cards show sign-in as reported by the tool, billing in full words and “Not checked yet”. The consent sheet lists exactly what is and isn't sent. Library cards state the last outcome in words with one “Next:” action. Running cards keep the red edge and Stop.

## 4. Visual hierarchy

- **Three levels per screen:** screen title (24 px, weight 650), section titles (20 px) and sub-heads (16 px). Body text is 14–15 px and secondary notes are 13 px. The smallest label is 12 px, used for captions, overlay tags and example markers.
- **Primary action:** the only filled blue button on a screen. Destructive or urgent actions use the filled red Stop. Everything else is outline or quiet.
- **Spacing:** 20–24 px panel padding, 16–20 px gaps, and generous whitespace kept from the study. Dense lists, such as steps and runs, use 8 px gaps between full-height rows instead of tables.
- **Screenshot first:** wherever a decision depends on the screen, the image is the largest element, and its explanation sits next to it, never in a tooltip.
- **Progressive disclosure:** “How it decides” and “Timing and details” stay collapsed under an “Advanced” hint. Match settings, coordinates and versions never appear on the main surface.

## 5. Component and state vocabulary

| Element | Look | Meaning |
| --- | --- | --- |
| Target | 2 px blue outline | What to find |
| Search area | White dashed outline | Where to look |
| Safe click area | Green tint with border | Where a click may land (position only) |
| Skipped | Red/black hatching | Never clicked, e.g. a badge |
| Preview points | White dots | Possible landing points; drawn only |
| Actual click | Amber ring | Where a past run clicked (replay only) |
| Candidates | Amber dashed, numbered | “More than one” matches |
| Status chips | Icon + word + tint | Found one ✓, More than one ⚠, Not found ⊗, Couldn't check / Effect unknown ? (dashed), Timed out ◷ |
| Takeover | Red frame + red run bar | The app is controlling input |
| Evidence | Lavender, stripes, film edge | Read-only past run |
| Illustrative screen | Grey hatched panel, dashed border, amber “Illustrative” badge | Invented follow-up game screen; never a real screenshot |
| Proposal | Pink dashed chip | An undecided option shown for discussion |

Document-level labels (Real screenshot, Illustrative, Example value, Proposal, External system UI) sit under every board title, so each board states what's real.

## 6. Screenshot fidelity

The only real image is `last-z-reference.png` (2000 × 1125). Every use is a CSS crop of that file with recorded source rectangles: the Mail close-up is [1700, 840, 300, 187.5] and the EXP region is [1150, 620, 400, 280]. The raster isn't edited. The Mail bounds [1930, 898, 60, 64], the badge, the safe area and the four EXP bubble positions were checked by eye on temporary inspection crops. They are drawn bounds, not detector output. The real frame stands in for the same moment wherever it appears: the step 3 frame, the re-record starting screen, the replay's “before” and the consent preview. Every later game state (mail panel, empty mail, new take, after-click) is a hatched illustrative panel with a visible label. The actual-click point in replay is labelled illustrative.

## 7. Accessibility

- State is never shown by colour alone: every chip has an icon and a word, the HUD states differ in shape, and unrecorded lanes are dashed.
- Text colours on dark surfaces follow the approved study tokens; secondary text uses `#aeb5be` on `#14171b`. Overlay tags are white on a near-opaque dark plate, so they stay readable over busy game art.
- Every screenshot region has `role="img"` and a descriptive label. Decorative icons are `aria-hidden`. Boards have `aria-labelledby` headings and stable anchors (`#b01`…`#b14`).
- Click targets in mockups are at least 32 px high, and primary and Stop buttons 36–44 px.
- There's no animation, so the document is fully static. The app spec should keep `prefers-reduced-motion` behaviour from the study.

## 8. Responsive document behaviour

Desktop composition comes first. Boards render at full width up to 1360 px, and the editor uses the approved two-column split (content plus a 340–380 px inspector). Narrower containers switch to documentary stacking through container queries, not a miniature desktop. Inspectors drop below the screenshot, flow lanes stack, and library cards go to 2 columns, then 1. The three preview-answer cards stay in three columns down to 560 px. The in-game HUD hides its optional parts below 900 px so it doesn't cover the game's own controls. No page height uses `vh`, and the outer wrapper has no background, border or side padding.

## 9. Decisions honoured, and what's left open on purpose

Honoured: offline core and optional AI, no account, X11 and Wayland as tested setups, attach-first with optional Steam launch and a separate ready check, independent copies only, position-only variation with “timing is never randomised” on the control itself, stop-and-explain as the default with pause and recovery as alternatives, no automatic retry of uncertain effects, a per-workflow debug toggle, read-only replay, own-login CLI billing kept separate from API billing, Jev as optional, online and experimental, and OpenRouter release scope as undecided.

Shown only as example or placeholder: countdown and timeouts (3 s, 5 s, 3 min), the 20% edge margin, the folder path, “new file each run” and “this run only”, the `stop key` placeholder, session names, workflows, times and values.

## 10. Questions for the user

1. **Action sentence:** should every action have a plain-sentence summary with colour-matched phrases, or should it appear only in the inspector?
2. **Replay colour:** is a dedicated lavender “evidence” colour acceptable as an addition to the approved palette, or should replay reuse the accent with stripes only?
3. **In-game recording pill:** is a pill in the game's empty band acceptable during recording and runs, or should the app stay entirely outside the game window?
4. **Flow as a list:** is the vertical branching list enough for the first release, with arbitrary graph connections left for later?
5. **Unrecorded paths:** should a run that reaches an unrecorded lane always stop and explain, as shown, or offer pause for help by default?
6. **Value-loss default:** after re-recording, is “keep the old read step after the new ones” the right recommended choice?
7. **Outputs:** which proposal should the next mockup show as default: new file each run, append, or ask each time? Should values carry across runs?
8. **Library “Next”:** is one suggested action per card helpful, or would you rather see plain status without suggestions?

## 11. Verification record

- `html_preview` (dark, 728 px): **overview-v1.html** was visually inspected. Content height was 1742 px, there were no console messages, and the screenshot inlined correctly. The previewed markup was the file contents minus two CSS comments.
- Local headless Chrome (file://, no server; screenshots kept in /tmp only): **overview-v1.html** at 728 and 1280 px, and **journey-v1.html** at 728 px (about 24,800 px tall) and 1440 px (about 19,400 px tall). I inspected these as slices, board by board. They found and fixed: flow inline text breaking, stretched buttons, library thumbnail crop mapping, the HUD covering game controls at 728 px, and oversized stacked answer cards.
- `html_preview` was **not** run on journey-v1.html because of its 140 KB size. Its visual check relies on the Chrome renders above, which use the same Chromium engine but don't include the host theme variables.
- Not checked: light appearance, and any widths other than those listed.

## 12. Known visual limitations

- Only one real frame exists, so most frames are labelled illustrative panels. The repeated real frame stands for “the same moment” and isn't proof of other screens.
- On the whole-frame views at 728 px, the Mail outline is small. The close-ups carry the detail.
- The “Not found” example uses a misplaced search area rather than a missing button, because the real frame contains Mail.
- Host-theme captions were checked in dark mode only.
- The overview is 1742 px tall at 728 px, near the top of the 1200–1800 px target.
