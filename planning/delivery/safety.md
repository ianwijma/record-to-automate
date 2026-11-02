# Safety invariants for every packet

[Delivery index](../DELIVERY-PLAN.md) · [Implementation plan](../IMPLEMENTATION-PLAN.md)


These apply to every packet. Each one has a case that tests it, cited in brackets.

| ID | Invariant |
| --- | --- |
| S1 | **Abort before dispatch.** The guard refuses dispatch until a direct abort pipe, successfully grabbed global chord, watchdog and parent-EOF handling are armed for the run. Its abort thread has its own X connection and never waits for capture, disk, vision, engine or UI. Native-server failure and physical overlap still require [P7.1](tasks/P7.1.md) qualification. [m0.guard.not-armed, m0.abort.*] |
| S2 | **A fresh observation for every action.** Each desktop-mutating action is authorized only by an observation whose earliest plausible content time is later than the previous action's settle boundary. One frame never authorizes two actions. [m0.guard.effect-seq, m0.engine.fresh-frame] |
| S3 | **Recipient, focus and geometry are checked at dequeue.** The guard uses its own X connection to check the window under the point (pointer actions) or the focused window (key actions) against the request’s expected client window tree, and checks that the window's geometry is unchanged. A mismatch rejects the action. [m0.guard.recipient-cover, m2.guard.recipient-focus, m0.guard.geometry-moved] |
| S4 | **Single-use dispatch with deadlines.** M0 uses one active run generation and a strictly increasing action sequence, with one request in flight. Each request includes an observation and capture-derived deadline. Reuse, an old generation, wrong sequence or expiry rejects it. A general permit service is deferred to [P7.1](tasks/P7.1.md). Timestamps are never renewed. [m0.guard.reuse, m0.guard.stale-deadline] |
| S5 | **Bounded, revocable dispatch.** There is one request slot and no backlog. A second request while busy rejects immediately. Abort, pause and stop invalidate the generation, discard pending work and release only guard-owned inputs. [m0.guard.busy, m0.abort.mid-hold] |
| S6 | **No uncertain retry.** A missing acknowledgment or an unknown settle becomes `OutcomeUnknown`. Automatic progress stops, and the action is never re-sent. [m0.engine.unknown-no-retry] |
| S7 | **No fallback.** Each action has exactly one locator. A visual miss never falls back to stored coordinates or another method. [m1.image.no-fallback] |
| S8 | **Only target input is kept.** The observer classifies each raw event as target, non-target or indeterminate **inside the native process**, before any stdout, log, IPC, persistence or crash output. Only target events leave the process. Indeterminate events are dropped and reported as gaps. The process disables core dumps. [m2.observe.target-filter, artifact scan] |
| S9 | **Recording and running exclude each other.** At most one lease (record or run) exists at a time, and pending/in-flight work is settled or revoked before the next lease. [m2.lease.exclusive] |
| S10 | **No faked native evidence.** Mocks may test pure logic. Claims about input, timing, abort and pixels come only from the fixture ledger or a real X server. Physical-input claims come only from attended manual evidence ([shared §3](validation.md).4). |
| S11 | **No implied privilege or live targets.** Automated suites run only on a private Xvfb display. Real-session cases are attended and use only the fixture until D-TARGETS authorizes Chrome or Last Z input. |
| S12 | **Time is capture time.** Process arrival, encoding or fsync times, and FFmpeg pipe arrival in particular, are never used as observation time. An `Observation` can be built only from a capture interval reported by the helper, widened by the measured content-lag bound. [m0.clock.*] |

The artifact scan checks every application/helper output, log and project/run directory created by validation for non-target sentinels, decoded key sequences and event leakage. Ground-truth non-target fixture receipts and the driver’s secret sentinel live only in a separate ephemeral oracle directory, excluded from app artifacts and removed after comparison. The scanner itself reports only counts/IDs, never the sentinel. This avoids deliberately writing the sentinel into the files being checked. A leak fails validation (exit 1).

