# Application design

Status: selected design direction. The [product decisions](../DECISIONS.md) govern scope. The [visual journey](journey.html) shows the approved pages and states. Explicitly labelled examples and open choices remain undecided.

- **Full screenbook:** [journey.html](journey.html). It has 16 numbered boards covering all 14 stages, with every screen visible without clicking.
- **Visual overview:** [overview.html](overview.html). It shows four scenes and is 1776 px tall at 728 px wide.

## 1. Design thesis

The design uses a polished dark studio, generous spacing, screenshot context, Actions/Flow/Values tabs and progressive disclosure. Four principles guide the experience:

1. **Every step reads like a sentence.** The selected action is summarised in plain words, for example "Find the Mail button inside the bottom-right corner, then click in the green area of the button it found". Each coloured phrase matches its mark on the screenshot.
2. **Four answers, never a score.** Recognition reports *Found one*, *More than one*, *Not found* or *Couldn't check*. Runs add *Timed out* and *Effect unknown*. No percentages appear, because nothing has been measured on this game. Nothing is clicked unless exactly one target was found.
3. **Editing, control and evidence look different.** Editable marks have white handles and an "Editing" tag ([board 06](journey.html#b06)). When the app controls input, a red frame and a striped-edge bar with Pause, Stop and the emergency key are always visible ([board 11](journey.html#b11)). A past run is lavender, striped and dotted, has a crosshair for the actual click, and has no handles ([board 14](journey.html#b14)).
4. **One state per frame.** Two states that can't be on screen together never share an app frame. Each gets its own small frame with a label outside the app, such as *State B · Waiting for the ready screen* or *Frame D · After accepting*. This keeps incompatible states visually separate.

Every screen still has **one clear next action**.

## 2. Board inventory and journey coverage

| Board | Stage | States shown, each in its own frame where they conflict |
| --- | --- | --- |
| [01 First opening](journey.html#b01) | 1 | Welcome in three steps, empty library, offline, no account, AI off |
| [02 Setup](journey.html#b02) | 2 | Input access refused, illustrative system dialog, Ready / Needs your OK / Not tested yet, a separate stop key tested here |
| [03 New recording](journey.html#b03) | 3 | A: ready, with a window-only capture preview. B: launched from Steam, waiting for the ready screen. C: no matching window. D: window minimised |
| [04 Recording](journey.html#b04) | 4 | Bar over the game (above it when narrow). Recording, Paused (Resume + Stop), Stopped/saving, Emergency stop used (keeping the take is an open choice). Stop versus emergency explainer |
| [05 After recording](journey.html#b05) | 5 | A: processing or nothing recorded. B: first review of captured steps only, a suggested ready-wait and an "Add step · Read text" ghost row |
| [06 Edit an action](journey.html#b06) | 6 | Editing with the search area selected and handles. What Fixed / Picture / Text each follow. Answers: Found one, Found in a new place, More than one, Not found |
| [07 Flow](journey.html#b07) | 7 | Yes / No / Couldn't check lanes, an unrecorded path, the Read step marked "Added by you" |
| [08 Values](journey.html#b08) | 8 | Made by → value → used by, read area on the panel, preview read result, file outputs as a proposal, empty state |
| [09 Copy and re-record](journey.html#b09) | 9 | A: independent copy. B: bracketed range and start frame. C: before accepting, the kept read step needs review. D: after accepting, saved, Run blocked, Undo |
| [10 Run preparation](journey.html#b10) | 10 | A: ready. B: blocked by a step needing review. C: game not open. D: waiting for the ready screen. E: another run in control |
| [11 Live run](journey.html#b11) | 10 | Running bar over the game with a takeover frame and the in-app run bar. Pause / Stop / emergency explainer. Paused by you, Stopped by you, Emergency stop used |
| [12 Stopped](journey.html#b12) | 11 | Run 09:14: not found, nothing clicked, expected versus seen, inspect versus restart. Run 21:40: effect unknown, no retry |
| [13 Pause for help](journey.html#b13) | 11 | A: paused, Read → value → check. B: read again worked, with a separate Continue. C: still unreadable |
| [14 Runs and replay](journey.html#b14) | 12 | Run list, lavender replay, evidence marks, branch, values, version, no runs. "Same button, two modes" |
| [15 Settings and AI](journey.html#b15) | 13 | A: helpers with a window-only consent sheet. B: Claude Code signed out. C: offline with Jev allowed on a step |
| [16 Library](journey.html#b16) | 14 | Stopped, copy needing review, running, unavailable and empty cards with "Open workflow". Saved / Saving / Couldn't save headers |

## 3. Journey highlights

- **Getting started (01–03).** The welcome is unchanged. Setup now describes the stop key as *a separate key tested on your setup*, not a guarantee. New recording shows exactly what is captured: a crop of the Last Z window with a blue outline, the desktop's top bar outside it, and the text "Inside the blue outline". The Steam launch, readiness wait, missing window and minimised window each get a frame in which Start is disabled.
- **Recording (04).** One bar, with words and shapes for each state. Pause and Stop are always present. When paused, Resume and Stop are present. The emergency key appears as a placeholder chip. On a narrow page the whole bar moves above the screenshot rather than dropping a control.
- **First review (05).** The list contains only what you did: six clicks and one key, plus one dashed suggestion. Reading text from the screen is offered as an "Add step" row because the recording can't have made it.
- **Editing (06–08).** The editor has an explicit mode. A tool row picks Target, Search area or Safe click area, and the selected mark gets handles. "Search the whole window" is offered only for the search area. Preview sits apart and only draws. Three small frames show what each method follows. The Fixed spot shows a crosshair only and warns it won't follow a moved button. A fourth answer, "Found in a new place", is a labelled schematic, because the real frame can't show movement. Values draw the read area and preview what it read on the example panel.
- **Copy and re-record (09).** The re-record happens on an independent copy. Before accepting, keeping the old Read step is offered honestly: its area is drawn on the old and the new screen, and the new one is marked **Needs review**. After accepting, the copy saves, but **Run is off** until that step is reviewed. The same block reappears in board 10 State B and on the library card.
- **Running (10–11).** Preparation has five separate outcomes. During a run, a hand icon and "App in control · hands off" replace the recording dot, the bar has a striped red edge, and a red frame surrounds the game. Pause and Stop act at once. The emergency key is "a separate route that doesn't need this bar or the app window". After an emergency stop, the app says it stopped sending input and asks you to release any key that still seems held. It marks the interrupted step *effect unknown*.
- **When it stops (12–13).** Not found and effect unknown come from different runs, so they no longer share a screen. Pause for help shows the chain Read → value → check, so reading again is clearly an observation that repeats no clicks. Taking the mouse back is a separate, red-play **Continue run** button.
- **Looking back (14).** Evidence can't be mistaken for editing. It has no handles, a dotted lavender detected target, a hatched safe area used, a crosshair pinned to the run, and an "Evidence · read-only" tag on the picture.
- **Settings and return (15–16).** The consent sheet's picture is the same window-only crop as recording, with "a fresh screenshot of this window is sent each time the check runs, not this picture". Library cards add "Open workflow" next to one suggested action.

## 4. Visual hierarchy

- Screen title 24 px, section 20 px, sub-head 16 px, body 14–15 px, secondary notes 13 px. The smallest label is 12 px, and nothing is smaller.
- Only the primary action is filled blue. Stop is filled red. State labels sit *outside* app frames in host text: a small monospace chip ("STATE B") plus a bold name.
- 20–24 px panel padding, with 18 px in small state frames. On wide screens, related state frames sit side by side; at 728 px they stack, or sit two to three across when they are small picture comparisons.
- Wherever a decision depends on the screen, the screenshot is the largest element, and its explanation sits next to it.
- "How it decides" and "Timing and details" stay collapsed.

## 5. Component and state vocabulary

| Element | Look | Meaning |
| --- | --- | --- |
| Target | 2 px blue outline | What to find |
| Search area | White dashed outline | Where to look |
| Safe click area | Green tint and border | Where a click may land, relative to the found target |
| Selected mark | Blue ring plus white square handles, "Editing" tag | Can be changed now |
| Fixed screen point | White crosshair, no outline | Recorded position; doesn't follow anything |
| Recorded spot (moved) | Faint dashed outline | Where the button was recorded |
| Read area | Blue dashed fill; amber when it needs review | Where a value is read |
| Preview points | White dots | Possible landing points, drawn only |
| Candidates | Amber dashed, numbered | More than one match |
| Detected target (evidence) | Dotted lavender, double line | What a past run found |
| Safe area used (evidence) | Hatched green | The area a past run used |
| Actual click (evidence) | Yellow crosshair, "Run 09:14" pin | Where a past run clicked (illustrative) |
| Recording bar | Red dot, plain dark pill | Recording |
| Running bar | Hand icon, striped red edge, "hands off" | The app controls input |
| Takeover | Red frame around the game | Control, not danger |
| Lock row | Amber row with a lock icon | Run is off, and why |
| Needs review | Amber chip and amber row | A kept definition may not fit the new screen |
| Open choice | Pink dashed chip | Undecided; shown for discussion |
| State label | Mono chip plus bold name, outside the frame | Which state this frame shows |

## 6. Screenshot fidelity

The only real image is `last-z-reference.png` (2000 × 1125). Every use is a CSS crop: Mail close-up [1700, 840, 300, 187.5], EXP region [1150, 620, 400, 280] and **window-only [0, 25, 2000, 1100]**. Pixel inspection showed the desktop's top bar occupies y 0–24 and the window's own title bar starts at y 25. The text-target outline sits on the real "Mail" label under the icon. All bounds are drawn by eye; none is detector output. Every later game state is a hatched illustration with a visible label. The moved-target answer is a schematic with no game pixels.

## 7. Accessibility

Every state has an icon and words as well as colour. Evidence marks differ in *shape* from editing marks (dotted, hatched, crosshair), and bars differ by icon and wording. State frames carry visible text labels. Screenshot regions have `role="img"` and descriptive labels; decorative icons are hidden from assistive technology. There is no animation. Controls in the mockups are at least 32 px high.

## 8. Responsive document behaviour

Desktop composition comes first, up to 1360 px. Container queries handle narrow widths:

- Editor inspectors drop below the screenshot.
- State frames stack.
- Small picture comparisons stay two to three across.
- The in-game bar becomes a row above the screenshot below 900 px of container width, with a caption, so no control is ever hidden.
- Illustrative panels grow to fit their content.

The outer wrapper has no background or padding, and no page height uses `vh`.

## 9. Decisions honoured, and what's left open on purpose

**Honoured:**

- Offline core; optional AI with explicit selected data.
- No account.
- X11 and Wayland as tested setups, not promises.
- Attach first, with optional Steam launch and a separate screen-based readiness check.
- Independent copies only.
- Position-only variation, relative to the found target, with "timing is never randomised" on the control.
- Stop and explain by default, with pause for help and recovery as configured alternatives.
- No automatic retry of uncertain effects.
- Per-workflow debug.
- Read-only replay.
- Own-login billing kept separate from API billing.
- Jev optional, online and experimental; OpenRouter scope undecided.
- Ordinary Stop, Pause and the emergency route all act at once. No "stop after this step" option is shown.

**Example or placeholder only:** timings, margins, the stop key, the folder path, "new file each run", "this run only", session names, workflows and values.

**Open choices:**

- Whether an emergency stop keeps a recording take.
- Value scope and file modes.
- What happens when a second run starts.
- Retention.
- How long to wait for readiness.

## 10. Visual review and limits

The static overview was visually reviewed at 728px with the original PNG and full CSS. Additional primary checks covered re-recording, pause-for-help and AI settings at 728px, and the live-run view at 1440px. No console errors were reported. Structural checks cover links, board anchors, local assets and absence of behavior scripts.

These checks establish the design reference, not runtime capability or complete accessibility/usability certification. Other widths, actual input control, capture permissions, recognition and provider readiness require implementation and qualification.

## 11. Known limitations

- There is only one real frame. "Found in a new place" and every later screen are labelled illustrations.
- Over the real game, the floating bar overlaps part of the top-centre buildings at desktop width. It stays clear of the resource counters, side buttons and chat, but the band isn't perfectly empty.
- The sixteen-board journey includes separate frames for conflicting states; it is a reference document rather than a required onboarding tour.
