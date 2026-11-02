# Astra review of Opus v1

Reviewed 2 November 2026 against `planning/design/BRIEF.md`, `planning/DECISIONS.md`, and the approved `planning/mockups/editor-visual-studies.html`. Scope: the independent visual proposal, not application implementation. Recommendations below are not approved product choices.

**Verdict: retain the direction; revise the specific promises, controls and layouts below before final comparison.** All fourteen journey stages have substantive boards. This is a coherent expansion of the approved dark studio, with particularly effective screenshot explanation and evidence styling. Findings: **1 High, 6 Medium, 0 Low**. High means a misleading assurance at a consequential decision; Medium means a material clarity, coverage or visual usability gap. These are design findings, not claims about runtime bugs.

## Reviewed versions

SHA256, relative to `planning/design/opus/`:

| File | SHA256 |
| --- | --- |
| `design-v1.md` | `204e6c115572935d29e18d86634536093e7f46ca0b86379e3d28b683bcaea5db` |
| `journey-v1.html` | `c983909f2e0ea807879a33495ed32be5cf11558f4e77539fc625512ea8b86dcf` |
| `overview-v1.html` | `2fa4a26cc81d82d95054a3278c7dc594241368982a4d4cb5e5290749488906bc` |

## Evidence and visual scope

Read the rationale and the entire journey and overview sources, including CSS, all boards and notes. Rendered review used **only `html_preview`, dark appearance**:

| Document | Visually inspected |
| --- | --- |
| Full overview | **728 px**, all scenes A–C and footer; returned height **1742 px** |
| Journey, complete individual boards | **728 px:** 02 Setup, 03 New recording, 06 Edit, 08 Values, 09 Copy/re-record, 10 Run, 11 Failure/pause, 12 Replay, 13 AI, 14 Library |
| Journey, complete individual boards | **1440 px viewport:** 04 Recording, 06 Edit, 07 Flow, 09 Copy/re-record; document retains its authored 1360 px maximum width |
| Approved study | **1440 px**, initial Actions/Mail view |

Journey previews extracted whole board markup, retained the original inline CSS, SVG definitions and `.doc` wrapper, and used the existing local image paths. No layout overrides or screenshot substitutions. Each preview captured its full reported height and returned no console messages. Images were actually inspected; clean console output is not the visual evidence.

**Source-only:** journey boards 01 and 05, the journey's document header/index/footer, and all width/state combinations not listed above. The approved study's other interactive views were source reviewed, not exercised. No full-document journey render, light-mode check, native interaction, provider call, installation or server. Only this review file was written.

## Concrete strengths

- Spacious dark surfaces, readable typography, familiar Actions/Flow/Values organization and a screenshot-led desktop split remain faithful to the approved study. The overview is readable without shrinking a desktop screenshot into a miniature.
- Board 06's sentence, legend and distinct target/search/safe-area marks teach three different concepts clearly. Real EXP bubbles illustrate ambiguity without fabricated confidence scores; missing Mail is honestly a misplaced search region.
- Board 07 distinguishes **No** from **Couldn't check**, keeps recording time separate from execution order, and marks the unrecorded lane without inventing footage.
- Boards 10–12 distinguish control, stopping, uncertain effects and read-only evidence. The striped replay ribbon works through shape and wording as well as colour; unknown effects explicitly prohibit automatic repetition.
- Independent copies, discard/accept/Undo, denied access, unavailable windows, busy runs and save failure receive visible treatment. Invented follow-up game screens are visibly illustrative. File modes and value lifetime remain proposals; offline core, own-login eligibility and separate API billing are explained rather than silently assumed.

## Findings

These are observed contradictions or gaps against the confirmed brief/decisions. The corrections are proposed visual treatments, not newly approved defaults.

| ID | Severity | Board / element | Consequence and requirement basis | Exact visual correction |
| --- | --- | --- | --- | --- |
| **DAO-01** | **High** | **09**, selected value-loss fix: “Recommended: the check keeps working” | Retaining the old read step preserves a producer, but the new take clicks a different tab and its illustration has no unread count. The assurance implies the old read region still works in that changed screen. This undermines the required review of affected values/targets before accepting replacement. | Change the helper to **“Keeps the read step; its target needs checking on the new screen.”** Show the retained read as **Needs review**, its existing read region beside the illustrative new screen, and **Review read target**. Permit saving the replacement, but depict Run blocked until that unresolved read is reviewed. Keep accept/discard/Undo. |
| **DAO-02** | Medium | **11**, “Continue from this check”; related **07–08** value dependency | The paused check consumes `unread_count`, which an earlier Read step produces. Putting the game on the Mail screen does not explain how that missing value becomes available. The visible recovery path skips the very operation the user needs to understand. | Show **“Read the unread count again, then check”**, a small Read → value → check strip, and a freshly read / still unreadable result. State that this observation does not repeat preceding clicks. Keep Stop visible and distinguish the later resumption of desktop control. |
| **DAO-03** | Medium | **06**, Picture mode → “Same point every time / The spot you clicked when recording” | This describes a fixed recorded position inside the image-target mode, whose adjacent copy promises to find a moved button. A beginner cannot tell whether disabling variation also disables following the found target. Recognition and position-only variation are separate confirmed concepts. | In Picture/Text modes use **“Same point within the found target”**, with a marker inside its outline. Reserve **“Recorded screen position”** for Fixed spot. Add compact static Fixed/Text variants showing their distinct point/safe-area and text-to-find controls; no interactive prototype is needed. |
| **DAO-04** | Medium | **06**, screenshot editor and Not found remedy | “Move the area, or re-capture the target” has no visible corresponding affordance. The main board shows explanatory outlines, a margin slider and Preview, but no selected resize state or target/search editing controls. The required action-editing journey currently explains the result better than how a nontechnical person changes it. | Add a quiet **Edit target / Edit search area** tool row and one selected rectangle with resize handles plus a short instruction. Include **Recapture target**. Keep these distinct from **Preview on this screenshot**; retain the spacious screenshot and collapsed advanced details. |
| **DAO-05** | Medium | **04**, recording HUD states | The paused pill offers only Resume: there is no visible way to finish while paused. At narrow width the main recording pill also hides Pause with the optional metadata. An emergency key mentioned in notes does not provide the requested visible control path. | Keep **Pause + Stop** while recording and **Resume + Stop** while paused. At 728 px, hide the step counter or place the complete HUD beside the screenshot instead of removing a control. Preserve the demonstrated desktop placement clear of game buttons. |
| **DAO-06** | Medium | **09** top re-record panel and **08** illustrative read panel, **728 px** | Board 09 nests two columns inside an already narrow half-width panel: the starting frame becomes a tiny thumbnail, the timeline reads “0:00drag…”, and Start re-recording exceeds its panel's intended right padding. Board 08's illustrative Mail panel is clipped at its bottom. These are rendered readability/spacing defects, not stylistic preferences. | Stack copy and re-record panels at 728 px; put the timeline instruction on its own row and give the starting frame full panel width. Let the illustrative panel grow to its content height or stack the read-method column below it. Retain the desktop composition. |
| **DAO-07** | Medium | **13**, consent preview labelled “Game window only”; **03** capture scope | The outgoing-data preview uses the full reference image, including the desktop's top system panel, while claiming only the game window is sent. The consent picture and exclusion list disagree about the selection. This is an evidence/crop-label problem; no new game capture is needed. | CSS-crop the existing reference to the selected game window, excluding the desktop panel, and outline that capture boundary. Label it **“Example of selected window; a fresh screenshot is sent when this check runs.”** Use the same scope depiction in recording setup. |

## Optional preferences — not blockers or approved choices

- Keep lavender replay styling unless the user prefers another treatment: the existing stripes and explicit read-only wording already carry its meaning.
- Consider a quiet **Open workflow** link on ordinary library cards, alongside the suggested Next action, to make returning to editing unmistakable.
- The action sentence is useful enough to retain. Whether it appears in every list row or only the inspector remains a presentation preference. Keep the confirmed stop-and-explain default while discussing any explicitly configured alternative.
