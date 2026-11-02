# Merge decisions for the combined implementation plan

Sources: [Astra v2](astra/plan-v2.md), [Opus v2](opus/plan-v2.md), [Astra responses](astra/responses-v2.md), [Opus responses](opus/responses-v2.md), and both [AO](reviews/astra-on-opus-v1.md)/[OA](reviews/opus-on-astra-v1.md) reviews. The user-confirmed baseline remains [DECISIONS.md](../DECISIONS.md). These are synthesis recommendations, not new user approvals.

| ID | Topic and source | Combined-v1 disposition and reason |
| --- | --- | --- |
| M01 | Native language: Astra C++; Opus Rust/C++ comparison | Adopt shared isolated guard/capture/vision contracts. Select language after binding/build/packaging evidence and user approval; no unmeasured language winner or unnecessary third language. |
| M02 | Process ownership: Astra guard contract; Opus explicit process table | Separate input guard scheduling from all slow services. Main has direct in-app abort; global abort is independently qualified. No killed-process cleanup guarantee. |
| M03 | Freshness: Astra complete authorization evidence; Opus deadline/refresh records | Adopt capture-time/uncertainty-derived deadline over all still-active predicates, checked after durability and queueing. Require recoverable assets for new confirm evidence. Do not inherit Opus's metadata-only ambiguity or assume target-pixel matching refreshes semantics. |
| M04 | Semantic lifetime: both revised plans | Clarify branch history versus active action preconditions. Avoid treating every past branch as an expiring permanent permission. Rerun complete semantic judgments unless a local check proves every relevant predicate; context-region shortcuts require separate qualification. |
| M05 | Wayland: both candidate matrices | Require event-time accelerated coordinates and recipient for actual ordinary input. XWayland-only and cursor-frame techniques are partial/corroborating, not full support. No fixed GNOME/distribution version selected. |
| M06 | Broker/key capture: both revisions | Narrow authorized session/device/mode/target scope, filter before forwarding/persistence and test revocation/overflow. No silent all-app keyboard capture or release waiver if recipient evidence is unavailable. |
| M07 | Key/text: Astra model and Opus correction | Preserve platform-tagged physical keys; explicit confirmed text only with demonstrated layout/IME capabilities. No automatic printable-key-to-text conversion. |
| M08 | Locators: Astra single method; Opus authored fallback chains | One selected locator initially. Omit fallback-chain editor as unrequested scope. Recorded coordinates never authorize fallback from failed recognition. |
| M09 | Geometry: Astra rectangles/discrete sampler; Opus scanline adoption | Recommend rectangular safe areas/exclusions first; quantize at actual backend precision, validate inverse containment, use compact scanline intervals rather than a large point list. Same preview/runtime implementation, position only. |
| M10 | Storage: shared directory-canonical model | Recommend immutable files/assets plus rebuildable index. Explicit file/directory/head ordering, checksums, active staging roots and tested filesystem scope. Probe is not proof of power-loss durability. |
| M11 | External outputs: Astra logical output identity; Opus create-new adoption | Recommend gated create-new initially. Use stable `(run, occurrence, output slot)` across attempts, journal destination/hash before writing, reconcile publication-before-ack. Do not use a changing attempt identity or silently retry an unknown effect. |
| M12 | Re-record: both ID/output corrections | Confirm step/target/output identities and meaning separately. Type equality alone does not preserve identity. Atomically commit mapping/binding repairs; independent copies and immutable evidence remain separate. |
| M13 | Recovery: Opus exits; Astra durable-budget refinement | Structured bounded policy with retry/continue/stop, counters per occurrence/run surviving resume and retry. No arbitrary cycles; unknown side effects prohibit automatic retry. |
| M14 | Debug: Astra all-mode intent; Opus coverage table/Runs boundary | Require every debug attempt/check/action and post-action evidence, plus minimum all-mode authorization/reconciliation evidence. Failures/gaps honest; no guaranteed persistent logs when all storage fails. Replay has no dispatch capability. |
| M15 | Providers: both exact-profile gates | Retain unmodified own-login CLIs and typed assistance. Initial authoring-only CLI scope is a proposal; optional runtime Jev stays release-required. Exact version/profile/image/config isolation must coexist with eligible auth; no copied tokens or hidden API billing. |
| M16 | Jev: Astra conservative complete-predicate proof; Opus context proposal | Keep same-observation presence/uniqueness/judgment/Box, with complete semantic revalidation. Defer context-region inference until qualified. Qualification can fail on animation; surface that before enabling unsupported actions. |
| M17 | OpenRouter: both conditional scope | User confirmed the provider identity, not release priority. A selected release adapter must be complete and tested; no stub counts as support. |
| M18 | Work packages: Opus granularity; Astra strict release closure | Use one dependency graph with early parallel proof, vertical slices, explicit acquisition/debug/Settings/CLI/Jev packages, dual-session release, and requirement-to-evidence rows. No provider or Wayland work lost from dependencies. |
| M19 | Sampling/corpus/defaults: both revised evidence rules | Remove speculative shipping numeric defaults and arbitrary backend failure timeboxes. Agree experimental tolerances/budgets before each test; report sample size and limitations. |
| M20 | UX/release choices: approved baseline; both proposals | Preserve approved spacious dark organization and add Runs consistently. Keep framework, storage/outputs, retention, license, package/update policy and privileged setup user-gated. No new screenshot or approval is needed just to complete this planning/review cycle. |

## Review handoff

Both models will review the same immutable `combined-v1.md` independently in fresh tasks. Reviews must assess the synthesis itself, including new clarifications M03/M04/M09/M11, not merely accept the authors' own preferred passages. The final revision will record accepted/rejected findings and remaining runtime/user gates.

## Post-review iteration

The first combined reviews are preserved. Their complete dispositions are in [REVIEW-RESOLUTIONS.md](REVIEW-RESOLUTIONS.md). The following changes form the frozen combined-v2 confirmation target:

- **M21 lifecycle:** shared revoke/cancel/owned-release transition for pause, stop, abort and unknown effects; new epoch on resume; no recording during pause.
- **M22 evidence:** desktop-mutation sequence and settled-effect capture fence supplement age limits; bounded compound actions do not authorize another action's old evidence.
- **M23 decisions:** explicit selecting-predicate scope through the arm's first side effect, including else/absence, read-only prefixes and nested arms; finite refresh/reselection budgets and snapshot-value distinction.
- **M24 early proof:** provisional G-L includes full durable capture-to-input latency before production choices. Experimental E gates and final Q defaults remove circular requirements; one user-approved protocol governs cell logs.
- **M25 platform:** required versus reported cells, explicit native-Wayland/XWayland coverage, recipient query/feed source, all-backend target-only filtering, and overflow/loss stop/new-take semantics.
- **M26 authoring:** minimal editor W09a precedes Slice A. Automatic image-target drafts and batch flags are recommended behind D-SEMANTICS; no routine per-click approval is forced.
- **M27 providers:** shared Settings/framework W15s permits independent CLI/Jev/router delivery. Exact child environment/auth-source and version requalification protect the declared own-login/billing path.
- **M28 outputs:** immutable payload/hash per logical output identity; bounded journal-owned staging cleanup. Write retries do not silently recapture changing screenshots.
- **M29 recognition:** D-RECOG assigns preapproved per-class adequacy criteria, report ownership and explicit failure alternatives; failed OCR is not relabelled successful region targeting.
- **M30 release:** explicit R19 abort/release and R20 unchecked-dispatch/unknown-effect invariants on both sessions; no weakened support claim by limitation label alone.

## Confirmation closeout

Both models judged frozen combined-v2 ready for user review. The final [implementation plan](IMPLEMENTATION-PLAN.md) incorporates Opus's small clarifications: decision scopes end at the first desktop mutation rather than file outputs; capture/settle uncertainty is compared conservatively; platform gates have setup/claim stages and X11-first experimental approval; output payloads are durable before intent; resume repeats current admission checks. [Review resolutions](REVIEW-RESOLUTIONS.md) record each disposition. No further model round is needed for these direct corrections; runtime proof and user-owned choices remain open gates.
