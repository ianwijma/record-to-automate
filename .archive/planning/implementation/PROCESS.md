# Implementation planning and review

Status: complete and ready for user review. Both models confirmed the frozen combined-v2 plan. The primary agent incorporated Opus's four remaining clarifications and optional resume-preflight note directly into the [final implementation plan](IMPLEMENTATION-PLAN.md). All findings have dispositions; no application implementation or runtime qualification was performed.

The user requested independent implementation plans from GPT-6 Astra high and Claude Opus 5.5 high, mutual review and revision, then one combined plan with a further review and iteration cycle. This process produces planning documents only.

## Requirements and design

- [Confirmed decisions](../DECISIONS.md) take precedence over architectural recommendations.
- [Existing product plan](../PLAN.md) supplies context; its implementation recommendations can be challenged.
- The selected [Opus v2 design](../design/opus/design-v2.md) and [journey](../design/opus/journey-v2.html) establish the visual direction and editor organization. Earlier frozen implementation-review snapshots predate this design selection.

User choices not yet made remain explicit decision gates. A recommendation in any implementation plan is not automatically user approval of a framework, helper language, storage format, license, timeout, or permission requirement.

## Rounds

| Round | Deliverables | Status |
| --- | --- | --- |
| Independent plans | `astra/plan-v1.md`, `opus/plan-v1.md` | Complete |
| Mutual review | `reviews/astra-on-opus-v1.md`, `reviews/opus-on-astra-v1.md` | Complete |
| Author revisions | `astra/plan-v2.md`, `astra/responses-v2.md`, `opus/plan-v2.md`, `opus/responses-v2.md` | Complete |
| Synthesis | `combined-v1.md`, `MERGE-DECISIONS.md` | Complete |
| Combined-plan reviews | `reviews/astra-on-combined-v1.md`, `reviews/opus-on-combined-v1.md` | Complete |
| Combined revision | `combined-v2.md`, `REVIEW-RESOLUTIONS.md`, merge addendum | Complete; runtime gates remain unproven |
| Focused confirmation | `reviews/astra-on-combined-v2.md`, `reviews/opus-on-combined-v2.md` | Complete: both ready for user review; no Blocker/High findings |
| Final revision | `IMPLEMENTATION-PLAN.md`, `REVIEW-RESOLUTIONS.md` | Complete: OV-01–04 and resume-preflight note incorporated directly |

Additional review rounds are warranted when a substantive revision leaves a blocking finding unresolved. Keep previous versions and dispositions so the user can inspect disagreements and how they were resolved.

## Review standard

Every finding identifies its severity, affected section or work package, concrete consequence, and a proposed correction. Reviewers distinguish requirement conflicts, implementation omissions, unsupported technical claims, sequencing problems, and preferences.

Authors record accepted findings and rejected findings with reasons. The combined plan records the chosen approach and tradeoffs rather than accumulating incompatible alternatives.

Completion requires both independent plans, mutual critiques and author revisions, a synthesized plan, both combined-plan critiques, and a revised combined plan. Blocking technical questions must either be resolved in the plan or expressed as concrete early feasibility gates with evidence and explicit stop/reconsideration criteria. User-owned choices stay visible rather than being silently selected.
