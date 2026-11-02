# Astra confirmation review of combined-v2

**Verdict: ready for user review.**

**Remaining/new findings: 0 Blocker, 0 High, 0 Medium, 0 Low. Unresolved Blocker/High PLAN issues: none.**

Target: [combined-v2.md](../combined-v2.md). SHA256: `82458fdf40ea2de35acae093d50f243ae3bfc9446c543fba5c50459b1e1e563b`.

Reviewed the entire frozen target, REVIEW-RESOLUTIONS.md, MERGE-DECISIONS.md including its post-review addendum, both combined-v1 reviews, and planning/DECISIONS.md. This is confirmation of the revised written contracts, not runtime certification. No other combined-v2 review was read; no delegation, implementation, installation, live testing, provider calls or configuration changes occurred.

## Previous finding dispositions verified

| Finding | Verification against the target |
| --- | --- |
| AC-01 / OC-08 | **Resolved, §6.3; W06/W09/W13.** Pause, ordinary/bounded stop, abort and unknown effects share revoke-first cleanup, cancellation and owned-input release independent of storage. Current abort availability gates admission. Pause collects no recording input; resume requires a new epoch and fresh readiness. Unknown effects require reconciliation. |
| AC-02 | **Resolved, §10; W09a/Slice A; R05.** The minimal desktop editor and run controls precede Slice A, with D-UI before renderer work. Save/reload and changed fixture execution are explicit acceptance, so W10 no longer supplies its own prerequisite. |
| AC-03 | **Resolved, §4; W02/W08/W14; R04.** Event/recipient loss or overflow stops recording, clears unresolved correlation and dependent input state, and leaves incomplete actions non-runnable. A qualified new-take boundary precedes restart; missing edges/recipients are never fabricated. |
| OC-01 | **Resolved, §6.2; W01/W06/W09; R20.** Desktop-effect sequence and settled-effect capture fences supplement age. Guard rejection after dequeue prevents earlier-action evidence reuse. Bounded compound subcommands cannot authorize another action; no non-interference exemption is enabled. |
| OC-02 | **Resolved, §6.1; W05/W09/W16.** DecisionScope specifies expected outcomes, else/absence, enclosing scopes, read-only prefixes, first-known-effect consumption, empty arms and stored-value semantics. Refresh re-evaluates the selecting path within the same durable readiness budget; unknown outcomes cannot select false or continue automatically. |
| OC-03 / OC-05 | **Resolved, §§2/10.** Provisional G-L in W01/W02 measures the complete durable capture-to-dispatch path. E envelopes permit implementation; integrated measurements inform Q gates before Slice B qualification. No production implementation must already exist to supply its own experimental approval. |
| OC-04 / OC-06 | **Resolved, §9; W04/W15.** Sanitized child environment and effective settings/helper routing are covered. The CLI must report the active auth source under that profile; mismatch/unverifiability disables subscription use. Version qualification includes isolation, unchanged settings, images, cancellation and usage disclosure. |
| OC-07 | **Resolved, §6.2; W01/W02/W06/W14.** Recipient evidence comes from the guard's own bounded X11 queries or a qualified Wayland query/feed, independently of permit expectations. Missing/stalled proof rejects dispatch without blocking abort. Deadline, epoch, uncertainty and residual-race tests are explicit. |
| OC-09 | **Resolved, §4; W08.** Target-only filtering applies to every recorder backend before persistence, renderer IPC, logs, diagnostics and application crash reports. Raw-service core-dump suppression is qualified without claiming control over administrator memory capture. |
| OC-10 | **Resolved, §4; W02/W14/W18; R01.** Required cells are distinguished from reported enhanced text/IME methods. Native-Wayland/XWayland coverage is an explicit support decision; partial evidence or a limitation label cannot pass a failed required cell. |
| OC-11 | **Resolved, §10.** W15s supplies common Settings/provider contracts without passing CLI prerequisites. W15, W16 and W17 depend independently on their own capability evidence. Promised CLI support still gates release, not Jev development. |
| OC-12 | **Resolved, §§2/7; W03/W07.** W00 user-approved criteria, implementer-owned per-class reporting and D-RECOG establish ownership and failure routes. Another target method cannot relabel failed OCR/image recognition as passing. |
| OC-13 / OC-14 | **Resolved, §§2/7; W00/W07/W08/W10.** One investigation protocol governs logged cell parameters. Qualified automatic image drafts avoid routine per-click approval; batch flags block unsafe drafts, and every runtime locates afresh. D-SEMANTICS still gates adoption. |
| OC-15 | **Resolved, §11; W18.** R19 and R20 require both-session evidence for abort/release, permit/freshness/effect fences and unknown-effect handling. Release closure cannot omit these safety contracts. |
| OC-16 | **Resolved by the documented alternative, §8; W12.** Immutable payload/hash per logical output identity removes changing-attempt hashes. Reconciliation precedes retry, uncertainty stops, and cleanup touches only contained, journal-owned inactive staging. |
| OC-17 | **Resolved, §§6/9/10.** Shortcut ownership is assigned; basic/debug and offline/online are distinct; W05 has no renderer prerequisite; unavailable secure storage leads to explicit session-only credentials or refusal to persist. |

## Cross-contract confirmation

All-mode recoverable authorization assets and intent precede permits; the final guard check follows writes and queueing. Earliest plausible source capture time, rather than arrival or persistence time, determines expiry. The desktop fence adds an independent invalidation condition without turning compounds into reusable authorization containers.

Decision scopes retain selecting evidence through reads/waits and consume it at a known first effect, including when later verification fails. Nested/empty-arm handling and pure stored-value snapshots do not imply permanent live-screen predicates. Popup dismissal, late else-path popup, unchanged button with changed context and slow Jev refresh are named acceptance cases. Bounded refresh/reselection cannot reset its readiness allowance.

Copying, re-recording and outputs retain separate identity/type/meaning checks. Branch joins and recovery-continue paths validate availability; failed-step outputs remain unavailable. Frozen output bytes and pre-attempt logical identity preserve reconciliation across retries without duplicate publication.

The package ordering is coherent. Early provisional proof and pure schema/store work can overlap, with native findings reconciled before production contract freeze. W09a precedes Slice A; common provider work does not depend on CLI success; integrated measurements precede Q qualification. Framework, native language, compositor/setup, output semantics, retention and licensing remain genuine user gates, not implied approvals.

## Review exit

No new AV findings are warranted. No additional full review round is needed; the root agent can handle any remaining small editorial notes directly. Guard-death release, event-time Wayland evidence, practical recognition/durability/freshness and exact provider profiles remain honest, named qualification gates. Their unproven feasibility does not block presenting this plan for user review or authorize implementation before the applicable decisions.
