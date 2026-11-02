# Opus focused confirmation review of combined-v2

Reviewer: Claude Opus 5.5 high, 2 November 2026. This is a review of the plan only. Nothing was implemented, installed or tested live, and no provider was called. I did not read the other model's combined-v2 review.

**Target:** [combined-v2.md](../combined-v2.md). Its SHA-256 is `82458fdf40ea2de35acae093d50f243ae3bfc9446c543fba5c50459b1e1e563b`, identical to the current `IMPLEMENTATION-PLAN.md`.

**Also read:** REVIEW-RESOLUTIONS.md, the MERGE-DECISIONS post-review addendum (M21–M30), both `*-on-combined-v1.md` reviews and DECISIONS.md.

## Verdict

**Ready for user review.**

| Severity | Count |
| --- | --- |
| Blocker | 0 |
| High | 0 |
| Medium | 1 |
| Low | 3 |

Every previous finding was corrected coherently, and the corrections did not introduce a major gap. The remaining notes are one-sentence clarifications that the root can make directly. No further delegate round is needed.

## Previous findings: disposition check

| Finding | Where the correction is | Verified |
| --- | --- | --- |
| AC-01 | §6.3: a shared transition that revokes the epoch first, then cancels queued work, releases owned inputs, reports uncertain effects and exposes the state. Cleanup does not wait for storage. Preflight checks the current abort route. Resume uses a new lease and epoch. SIGKILL is kept as a separate gate. | Yes |
| AC-02 | W09a is placed before Slice A and gated by D-UI. A changed action must survive reload. R05 traces it. | Yes; the work packages are topologically ordered |
| AC-03 | §4: any loss or overflow on any backend stops recording with a gap. Correlations are discarded and incomplete actions are not runnable. A new take follows a requalified boundary. | Yes. Choosing "stop and start a new take" is a conservative, valid choice of the two AC-03 options |
| OC-01 | §6.2: a guard-numbered effect sequence plus a settled-effect capture fence. There is no exemption for non-interference. Compound actions share an identity without becoming a loophole. Unknown outcomes never advance the boundary. | Yes; see OV-02 |
| OC-02 | §6.1: DecisionScope defaults. These cover else and absence, read and wait prefixes, nested and empty arms, consumption at the first known effect, halting on unknown outcomes, stored values versus live screen, and one non-resetting budget. The counterexample tests are listed. | Yes; see OV-01 |
| OC-03 | G-L runs inside W01 and W02 on provisional code, before W05 is frozen and before W09. | Yes |
| OC-04 | §9: an allowlisted environment, a routing and helper audit, an authentication source reported by the CLI without reading tokens, a billing display, and refusal on mismatch. API mode is never a fallback. | Yes |
| OC-05 | E and Q are split. E is built from provisional measurements; Q is built from W13 and W14 integrated measurements before Slice B. | Yes; there is no cycle. See OV-03 |
| OC-06 | A version floor or range, requalification started by the user, disclosure of account usage, and failure or cancellation leaving settings unchanged. | Yes |
| OC-07 | Recipient checks use the guard's own X11 connection, or a Wayland feed proven by W02, under the deadline. A stalled feed rejects input. Abort runs independently. | Yes |
| OC-08 | §6.3: no lease or input collection while paused. Re-recording requires stopping the run. | Yes |
| OC-09 | Filtering applies to every backend and every application output channel. Core-dump suppression is qualified, with no claim about administrator-level capture. | Yes |
| OC-10 | Required cells and reported cells are separated. Native-Wayland and XWayland coverage are approved by D-PLATFORM. No limitation can be waived; §4, §6.3, W14 and §12 are consistent. | Yes |
| OC-11 | W15s is independent of CLI success. W15, W16 and W17 each depend on it separately. Slice B still requires W15, or an explicit scope change. | Yes |
| OC-12 | D-RECOG criteria are set in the W00 protocol. The implementer reports. A failure never relabels region or fixed targeting as a pass, and Jev is not a substitute. | Yes |
| OC-13 | Automatic image drafts with batch flags, gated by D-SEMANTICS. Variation is off by default. | Yes |
| OC-14 | One W00 protocol plus a log of cell parameters, with escalation only on deviation. | Yes |
| OC-15 | R19 and R20 are required on both sessions. | Yes |
| OC-16 | The immutable payload choice is stronger than my suggestion to accept multiple hashes. Staging is owned by the journal, and publication is reconciled first. | Yes; see OV-04 |
| OC-17 | Shortcut work is assigned to W06, W09 and W14, with UI in W09a and W10. Non-debug and offline are separated. W05 has no renderer gate. A session-only key or a refusal to persist covers the missing-keyring case. | Yes |

The other focus areas also hold:

- **Ease of use:** one W00 protocol and batched target review, while D-UI, D-NATIVE, D-STORE, D-SEMANTICS and the other open choices still gate their production packages.
- **Outputs:** logical identity and meaning checks across re-record and copy, and output availability on recovery-continue paths (§5).
- **Honest gates:** the hard gate for guard death is stated honestly.

## Remaining findings

### OV-01: Medium. "First side effect" is ambiguous for file outputs

**Where:** §6.1 DecisionScope, §6.2 fence paragraph, §8; W05/W09/W12.

**Problem:** scopes are consumed at "the first side-effect occurrence in the selected arm". §8 calls a file output an "external side-effect". Meanwhile §6.2 says writing an already captured value does not mutate the desktop.

**Consequence:** read literally, an arm such as `if Jev "Shop screen" → save screenshot → click Buy` consumes the Shop predicate at the file write. The Buy click then relies only on its unchanged icon. That reopens the "unchanged button, changed context" counterexample that OC-02 was meant to close.

**Fix (one sentence in §6.1):** scope consumption occurs only at the first guard-dispatched desktop-mutating occurrence. File and value outputs inside the arm neither consume the scope nor advance the effect fence. Add the arm "output then click" to the W05/W09/W16 counterexample tests.

### OV-02: Low. The fence ignores timing uncertainty at the settle boundary

**Where:** §6.2 fence paragraph.

**Problem:** capture time is conservative ("earliest capture time"), but the settled-effect boundary has no stated uncertainty or clock domain. Acknowledgment and settle times come from the guard and engine clocks; frame times come from the mapped source clock.

**Fix:** compare the earliest plausible capture time against the latest plausible settled-boundary time in the calibrated monotonic domain. A boundary with an unknown bound blocks authorization, as unknown capture bounds already do.

### OV-03: Low. Two gates are ambiguous about when they apply relative to W02

**Where:** §2 D-PLATFORM and D-EXEC-E; W02, W06, W14.

**Problem:**

- D-PLATFORM is both a prerequisite of W02 (setup and client coverage) and blocks the support claim that depends on W02 and W14 evidence.
- D-EXEC-E draws on "W01/W02/W03" measurements, while G-L runs in W02 only "when available". So it is unclear whether a slow W02 blocks X11 work in W06, W07 and W09.

This is not circular, but it will be read inconsistently.

**Fix:** label the two stages of D-PLATFORM: candidate selection before W02, and support claim before W14 and W18. Also state either that D-EXEC-E waits for the W02 G-L results, or that it may be issued X11-first and then amended with Wayland data before W14.

### OV-04: Low. The frozen output payload is not explicitly durable before intent

**Where:** §8, second outputs paragraph; W12.

**Problem:** "A write retry reuses those bytes" assumes the bytes survive a crash. If the payload exists only in memory, recovery after a crash cannot retry. The rule against recapturing correctly forces a stop, but the intended retry path silently becomes unavailable.

**Fix:** persist the frozen payload as a content-addressed asset, with durability barriers, before journaling the output intent. Otherwise, document that loss of the payload stops for reconciliation.

**Optional, not counted:** state that resume re-runs run-admission preflight under the new lease, covering the current abort route and recipient source. The abort route could be revoked while the run is paused.

## Exit

There are no Blocker or High plan issues. The root can apply OV-01 to OV-04 directly as wording or contract clarifications, without another review round. Runtime feasibility and the user-owned D gates remain explicitly gated, as they should be.
