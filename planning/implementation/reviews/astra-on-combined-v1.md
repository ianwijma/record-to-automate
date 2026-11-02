# Independent review: Astra on combined-v1

**Verdict: ready after listed changes.** Findings: **0 Blocker, 1 High, 2 Medium, 0 Low**. The synthesis needs focused contract and dependency corrections, not a new architecture. Unselected user-owned choices and honestly gated runtime feasibility are not planning blockers.

Reviewed combined-v1, MERGE-DECISIONS, DECISIONS, PLAN, and both authors’ complete plan-v2/responses-v2. Neither source plan was treated as canonical. No other combined review was read. No implementation, installation, configuration changes, or live platform/provider tests were performed. Findings below concern the written plan; proposed validation is future evidence.

## High-value choices retained

- **Release closure is substantive.** W18 reaches both required Linux sessions, completed Claude/Codex Settings and adapters, optional-at-use runtime Jev, and conditional OpenRouter through Slice B. Required failures cannot become passes merely by receiving limitation labels. Structured screen branches, independent copies, meaning-aware re-record mappings, recovery availability and durable counters preserve the confirmed requirements without adding workflow calls or fallback chains.
- **M03/M04 resolves a real semantic distinction.** Historical branch selection need not remain visually true forever. Dismissing a popup can consume its presence predicate, after which a freshly established menu precondition authorizes the next action. Conversely, a still-active AI judgment cannot be refreshed by finding an unchanged button after its surrounding screen changes. The schema/lifetime work and complete-predicate refresh rule are implementable; W05/W09/W16 should preserve both counterexamples in acceptance. An intervening navigation action must not by itself discard a semantic predicate still needed by a later effect. This is validation of the stated rule, not a demand to keep all historical branches perpetually true.
- **Evidence and effects have useful boundaries.** Capture intervals/uncertainty/epochs, complete recoverable authorization assets in every mode, final post-durability/post-queue checks, single-use permits and unknown-effect reconciliation fit together. Stable output-slot identity precedes attempts; directory ordering, active staging roots, no-replace publication and limited filesystem claims avoid the earlier durability shortcuts. Debug covers action/check/locator pre-evidence and post-action evidence; replay has no dispatch authority.
- **Unproved capabilities remain gates.** Actual event-time recipient/coordinates, ownership-safe helper-death release, practical evidence-to-input latency, exact CLI isolation with eligible authentication/images, and useful fresh Jev decisions require evidence. The plan does not claim those tests have passed. Local recognition/offline operation, position-only variation, OS-backed credential storage and refusal of unsupported provider profiles remain appropriate constraints.

## Findings

### AC-01 — High — Pause and ordinary stop lack an explicit guard transition

**Target:** §6.3, especially paragraphs beginning “One recording or execution lease” and “Default on bounded failure”; W06/W09/W13; R14.

**Defect and consequence:** The text explicitly revokes pending work for lease expiry, engine death, source/abort-route loss and reconnect. It explicitly revokes/releases after post-dispatch storage failure in §8. It does not require the same transition for an ordinary user pause, bounded-failure stop, or entry into `OutcomeUnknown`. “Release only guard-owned held inputs” limits ownership but does not specify when release happens. An implementation can therefore stop advancing the interpreter while an already queued permit remains dispatchable or a physical-key-down action remains held. The engine stays alive, so its watchdog does not resolve this. A later maximum hold bound would not make pause-for-help safe at the moment it is offered.

**Correction:** Define a shared transition for pause, stop, abort and unknown outcome: invalidate the lease/permit epoch first; reject queued work and interrupt cancellable submissions; release only proven guard-owned holds; report any already-submitted non-revocable effects or uncertain release; then expose the resulting state. Cleanup must not await evidence writes. Resume requires a new lease plus fresh readiness/predicates, and unknown outcomes additionally require reconciliation. Explicitly check the current independent abort route before admitting a run, not only its historical backend qualification.

**Validation evidence:** W06/W09/W13 scenarios pause and stop during a drag/key hold, with a queued command and stalled storage/provider. Show zero later dispatch from the revoked epoch, measured cleanup, preservation of overlapping physical-user holds, and rejection of old permits after resume. An unavailable abort route must prevent run admission. Keep guard SIGKILL as its separate conditional proof gate; this correction does not assert that a killed helper can release anything.

### AC-02 — Medium — Slice A requires an editor that depends on Slice A

**Target:** §10, Slice A and W10; W05/W08/W09; R05/R16.

**Defect and consequence:** Slice A promises “record → edit → save/reload → run,” but its prerequisite closure provides schema/store, recorder and engine, with no assigned editing surface. W10 is the first editor package and depends on Slice A. Delivering the promised user journey therefore introduces an implicit cycle or forces unplanned UI work into another package. Using a hand-edited JSON fixture would demonstrate an engine path, not the described desktop editing journey.

**Correction:** Split out a small pre-Slice-A editor/run-control deliverable, with D-UI before production renderer work, covering recording review, a basic action/target edit, save/reload and run/stop. Let W10 expand it into the approved full Actions/Flow/Values experience. Alternatively, explicitly redefine Slice A as a harness milestone and place the first user-editable journey after W10; do not claim UI editing at both stages without an owner.

**Validation evidence:** A topological package walkthrough must place every Slice A deliverable before the milestone. Its acceptance should name the actual editing mechanism and demonstrate a changed action surviving reload and affecting execution. Full Runs inspection can remain W13; saved debug evidence alone suffices for the early milestone.

### AC-03 — Medium — Broker buffer overflow has no fail-closed recording contract

**Target:** §4 optional broker paragraph; W02/W08/W14; R04; MERGE-DECISIONS M06.

**Defect and consequence:** The synthesis specifies bounded volatile correlation buffers and rejection of non-target/indeterminate events. It omits the overflow behavior and test promised by M06. Silently dropping a key-up or recipient-transition record could leave the remaining stream looking complete, producing an incorrect hold or assigning later events using broken correlation state. Bounded memory alone does not define whether the resulting recording remains runnable.

**Correction:** Overflow or loss in either the device-event or recipient-evidence stream must create an explicit gap, discard affected unresolved correlations, invalidate dependent gesture/key state, and prevent automatic promotion of an incomplete action into executable intent. Resume extraction only after a defined resynchronization boundary; otherwise stop recording with an explanation. Do not reconstruct a missing recipient or key edge from the last known focus/state. Apply equivalent gap semantics to other recording backends where events can be lost.

**Validation evidence:** W02/W08/W14 deliberately saturate each buffer and delay the consumer while exercising focus changes, chords and drags. Verify bounded memory, visible gaps, no forwarded/persisted indeterminate or non-target events, no fabricated complete gesture, and no silently executable incomplete hold. Revocation during overflow must still clear volatile state and close access.

## Review exit

Resolve AC-01 before lifecycle implementation and AC-02/AC-03 before accepting their affected milestones. Preserve the existing early feasibility gates and user-owned decisions. These findings require no live tests or additional user approvals to correct the plan; runtime claims remain pending their named evidence.
