# Combined plan review resolutions

Status: complete. Both combined-v1 reviews and the focused combined-v2 confirmations are dispositioned. Both models judged combined-v2 ready for user review; the remaining clarifications are incorporated in the final plan. These are corrections to the written plan, not verified runtime properties.

Target: [combined-v1](combined-v1.md). Revised document: [IMPLEMENTATION-PLAN.md](IMPLEMENTATION-PLAN.md). Earlier independent plans, AO/OA reviews and author responses remain unchanged.

## Astra findings

Source: [Astra combined-plan review](reviews/astra-on-combined-v1.md).

| Finding | Disposition | Revised location and correction | Remaining evidence |
| --- | --- | --- | --- |
| AC-01 High | Accepted | §6.3 adds one explicit pause/stop/abort/unknown transition: revoke epoch first, cancel queued/cancellable work, release only owned holds, report uncertain effects, then expose state. Cleanup cannot wait on storage. Current abort availability is preflighted; resume has a new lease and fresh state. | W06/W09/W13 pause/stop during holds/queued work with stalled storage/providers, physical-user overlap, old-permit rejection. Helper SIGKILL remains its separate unproven backend gate. |
| AC-02 Medium | Accepted | §10 adds W09a minimal desktop editor and run controls before Slice A; D-UI applies before that production renderer. Slice A names this actual editing surface; W10 expands it later. R05 traces both. | Topological work-package check and a changed action surviving save/reload and affecting fixture behavior. |
| AC-03 Medium | Accepted | §4 stops recording on event/recipient loss or overflow, emits gaps, clears unresolved correlation/gesture state and leaves incomplete actions non-runnable. Restart is a new take after a qualified state/clock/recipient boundary. R04 includes loss semantics. | W02/W08/W14 buffer saturation, focus/chord/drag cases, bounded memory, no fabricated actions/non-target persistence, revocation clearing access/state. |

## Opus findings

Source: [Opus combined-plan review](reviews/opus-on-combined-v1.md).

| Finding | Disposition | Revised location and correction | Remaining evidence |
| --- | --- | --- | --- |
| OC-01 High | Accepted, conservative initial scope | §6.2 adds a guard-visible desktop-mutation sequence and settled-effect fence. A new action's screen evidence must be captured after the previous settled desktop effect; no initial author-declared non-interference exemption. Compound subcommands remain one bounded action. | W01 G-L/W06/W09 overlay A then covered B, source clocks, delayed queues, compound boundaries; R20 on both sessions. |
| OC-02 High | Accepted with explicit stored-value and nested-arm semantics | §6.1 defines DecisionScope over selecting predicates/expected outcomes, including else absence, through read-only steps to first effect. Refresh/reselection consumes one durable budget; no old-path authorization. Scope consumption, empty/nested arms, snapshot-value versus live-screen checks are explicit. | W05/W09/W16 popup-dismiss, late popup/else, intervening read/wait, changed context, slow Jev and bounded refresh. |
| OC-03 High | Accepted | §2/W01/W02 add provisional G-L measuring the full durable evidence-to-input path before production store/engine commitments. | Reports with distributions, denominators, expiry rates, encoding load and candidate filesystems; return infeasible options to the user. |
| OC-04 High | Accepted | §9 includes allowlisted child environment and effective routing/helper audit, CLI-reported active authentication source under the exact sanitized profile, explicit billing display and refusal of unverifiable/mismatched subscription routes. | G-A-CLI sentinel variables/settings/helpers and unchanged parent/user configuration; no secret values logged or token copying. |
| OC-05 Medium | Accepted | §2 splits D-EXEC and D-EVIDENCE into experimental E envelopes from early proof and final Q profiles before Slice B. §10 applies E to production/test slices and Q to final qualification. | Gate/dependency walkthrough; final measurements come from the integrated implementation rather than being required to build it. |
| OC-06 Medium | Accepted | §9 profiles have tested floors/ranges and user-started exact-version requalification, with usage disclosure. Fail/cancel/version mismatch disables only the affected adapter with an explanation. | Version-bump fixtures for pass/fail/cancel, sentinel isolation, active auth source and image/cancel capability checks. |
| OC-07 Medium | Accepted | §6.2 defines independent bounded X11 backend recipient queries and a qualified Wayland query/feed with timestamp/uncertainty/epoch/sequence. Missing/stalled proofs reject input; abort cannot block on them. | W01/W02/W06/W14 focus theft before dequeue/dispatch, feed stall and transient/modal recipient rules; measured residual race. |
| OC-08 Medium | Accepted; overlaps AC-01 | §6.3 shared revocation/release transition plus no recording/input collection while paused, new lease/epoch on resume, no pre-pause authorization. | W06/W09/W13 pause-mid-drag, user interaction, resume, zero stale dispatch/recorded manual input. |
| OC-09 Medium | Accepted with application/OS boundary qualification | §4 applies target-only pre-forwarding/persistence filtering to every recorder backend and all app outputs. Qualify core-dump suppression for raw-event services; no claim to control administrator OS memory capture. | Non-target key sentinels across events, renderer IPC, logs, diagnostics and app crash reports; overflow/revocation tests. |
| OC-10 Medium | Accepted as explicit support-scope decision | §4 classifies required pointer/physical-key/capture/dispatch/transform/abort/release cells versus reported enhanced text/IME methods. D-PLATFORM approves native-Wayland and XWayland client coverage; XWayland-only does not silently complete the broader release. | W18 matrix names cell class/outcome and actual client backend; required failures need user scope change, not feature disabling. |
| OC-11 Medium | Accepted | §10 adds W15s shared provider/Settings contract. W15 CLI, W16 Jev and conditional W17 router depend on it independently and on their own feasibility cells. | DAG check shows Jev/router remain buildable with a failed CLI profile; release still requires promised CLI support or explicit scope change. |
| OC-12 Medium | Accepted | D-RECOG and §7/W03 assign adequacy criteria to the user-approved protocol, measured per-class report/recommendation to the implementer, and explicit failure/reconsideration routes. | Another local engine or explicit user-selected target/scope change; fixed/region is never relabelled a passed OCR cell, Jev never substitutes for offline core. |
| OC-13 Medium | Accepted as user-gated default recommendation | §7 recommends automatic image drafts from lossless pre-frames with source self-tests and batch flags. Unflagged qualified targets need no per-click confirmation; flagged actions block preflight. Variation remains off without a confirmed safe area. | W07/W08/W10 zero-edit fixture run and flagged-target refusal; source self-test does not replace current-screen runtime location. D-SEMANTICS approval remains. |
| OC-14 Medium | Accepted | §2/W00 use one user-approved spike protocol. Implementer logs cell parameters within that envelope, escalating only deviations/failures/privileged setup or user-owned choices. | W00 protocol and per-cell parameter log, without 15–25 approval interruptions. |
| OC-15 Medium | Accepted | §11 adds R19 abort/owned-release and R20 permit/freshness/effect-fence/unknown-effect invariants in dual-session release closure. | W18 links actual evidence for each safety row on both sessions; residual labels cannot satisfy required cells. |
| OC-16 Low | Partially accepted; immutable-payload rule chosen | §8 names owned staging files by logical identity/attempt, limits cleanup to journaled inactive files and reconciles publication before retries. Rather than accepting changing screenshot hashes under one effect, freeze the immutable payload/hash; recapture is a new explicit action occurrence. | Kill/publication/ack boundary tests: one final output, no duplicate write or abandoned owned staging. A retry uses original bytes only when prior publication is proven absent. |
| OC-17 Low | Accepted | §10 assigns shortcuts to W06/W09/W14 and setup UI to W09a/W10; §6 separates non-debug from offline; W05 pure model/store can start after W00 without D-UI, reconciling native findings before contract freeze; §9 offers session-only keys or refuses persistence when secure storage is unavailable. | Package/DAG/wording review; missing-keyring fixture; no implicit plaintext persistence. |

## Focused confirmation round

Both models independently checked the frozen [combined-v2.md](combined-v2.md) in fresh review tasks, with emphasis on AC-01–03, OC-01–17 and any contradictions introduced by the corrections. The target SHA256 was `82458fdf40ea2de35acae093d50f243ae3bfc9446c543fba5c50459b1e1e563b`. The frozen target and original review artifacts remain unchanged.

[Astra's confirmation](reviews/astra-on-combined-v2.md) verifies all previous dispositions: **ready for user review**, with zero findings at every severity. [Opus's confirmation](reviews/opus-on-combined-v2.md) also verifies all 20 previous dispositions: **ready for user review**, with zero Blocker/High findings, one Medium and three Low clarifications. Opus explicitly recommends direct primary-agent corrections without another delegate round. Those corrections are recorded below; no unresolved finding remains in the final written plan.

| Finding | Disposition | Final correction and verification requirement |
| --- | --- | --- |
| OV-01 Medium | Accepted | §6.1 defines scope consumption at the first guard-dispatched desktop mutation only. File/value outputs neither consume the scope nor advance/reset the desktop-effect fence; §6.2 agrees. W05/W09/W12/W16 include output-then-click with changed context. |
| OV-02 Low | Accepted | §6.2 compares earliest plausible capture against latest plausible settled boundary in one calibrated monotonic domain. Unknown settle bounds block authorization; uncertainty is included in fixture tests. |
| OV-03 Low | Accepted | §2 stages D-PLATFORM as S (setup before W02) and C (evidence-based intended claim before W14, final qualification before W18). D-EXEC-E can be X11-first, then must be amended from Wayland evidence before W14. Work packages and delivery path make that independence explicit. |
| OV-04 Low | Accepted | §8 persists the immutable output payload as a content-addressed asset with file/directory durability before journaling intent. Retries reuse durable bytes; absent/corrupt payload stops for reconciliation. Fault tests include payload/intent boundaries. |
| Optional resume note | Accepted | §6.3 repeats run-admission preflight on resume, including current abort route and recipient source. Tests include route revocation during pause. |

These final changes clarify the reviewed contracts rather than introduce a new architecture. Another review round is unnecessary; runtime tests remain assigned to implementation work packages.

## Verification status

No application implementation, live desktop/provider tests or runtime qualification took place during this planning process. User-owned D gates and unproven platform/provider properties remain explicit.
