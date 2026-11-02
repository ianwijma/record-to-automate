# PW.2 Attended Wayland pixels and dispatch probe

[Delivery index](../../DELIVERY-PLAN.md) · [Milestone probes](../milestones/probes.md) · [Implementation plan](../../IMPLEMENTATION-PLAN.md) · [Requirement and W-package mapping](../coverage.md)

Read [execution rules](../execution.md) and [safety invariants](../safety.md) before executing. Consult [experimental defaults](../defaults.md) for implementation constants and [validation contract](../validation.md) when implementing or running acceptance cases. Follow linked dependencies for their contracts; execute only this packet.

- **Outcome**: classified cells (passed, failed with cause, or inconclusive) for ScreenCast pixels with timing, and for RemoteDesktop dispatch to an XWayland fixture and to Chrome running natively on Wayland, using fixture ledgers. Unproven event-time recipient observation/abort cells remain open with proposed next methods and block dispatch where required.
- **Depends on**: [PW.1](PW.1.md).
- **Files**: `probes/wayland/{portal.ts,README.md}` (throwaway probe code, not product), `fixtures/web-target/` (shared with [P3.1](P3.1.md) if it already exists, otherwise a minimal page), `validation/cases/probe-wayland/attended.ts`.
- **Steps**: the timebox is 12 executor-hours. Write the probe's cell parameters before testing. Then run attended: ScreenCast session and capture interval versus visual clock. Before any RemoteDesktop click, demonstrate an independent abort route, bounded single-use dispatch, current recipient/geometry and fresh observation on this backend. If these prerequisites are unresolved, collect pixels only and mark dispatch cells inconclusive; an attended operator alone is not an abort route. No unchecked input probe is allowed.
- **Contracts/errors**: probe code must not be imported by `packages/*`.
- **Acceptance**: `bun run validate --case probe.wayland.attended --attended` (manual, with measurement fields).
- **Artifacts**: `docs/evidence/pw2-wayland.md` and ledgers.
- **Out of scope / escalate**: the production Wayland contract ([P8.0](P8.0.md)).
