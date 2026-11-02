# PP.3 Jev fixture probe

[Delivery index](../../DELIVERY-PLAN.md) · [Milestone probes](../milestones/probes.md) · [Implementation plan](../../IMPLEMENTATION-PLAN.md) · [Requirement and W-package mapping](../coverage.md)

Read [execution rules](../execution.md) and [safety invariants](../safety.md) before executing. Consult [experimental defaults](../defaults.md) for implementation constants and [validation contract](../validation.md) when implementing or running acceptance cases. Follow linked dependencies for their contracts; execute only this packet.

- **Outcome**: cells for presence, uniqueness, box and latency on fixture frames only.
- **Depends on**: [PP.1](PP.1.md); explicit authorization to send fixture frames to Jev and `secret:jev`, reused if already given. Production D-AI scope is separate.
- **Files**: `probes/providers/jev.ts`, `validation/cases/probe-providers/jev.ts`.
- **Steps**: send fixture frames (no game or desktop pixels) for the moved, duplicate and absent scenes, and record the results and latency.
- **Contracts/errors**: never persist the key, and never send a frame that contains non-fixture content.
- **Acceptance**: `bun run validate --case probe.providers.jev` (`missing` without required authorization or secret).
- **Artifacts**: `docs/evidence/pp3-jev.md`.
- **Out of scope / escalate**: runtime use ([P9.3](P9.3.md)).
