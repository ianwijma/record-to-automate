# PW.0 Wayland candidate proposal for D-PLATFORM-S

[Delivery index](../../DELIVERY-PLAN.md) · [Milestone probes](../milestones/probes.md) · [Implementation plan](../../IMPLEMENTATION-PLAN.md) · [Requirement and W-package mapping](../coverage.md)

Read [execution rules](../execution.md) and [safety invariants](../safety.md) before executing. Consult [experimental defaults](../defaults.md) for implementation constants and [validation contract](../validation.md) when implementing or running acceptance cases. Follow linked dependencies for their contracts; execute only this packet.

- **Outcome**: a one-page proposal for the user. It names the candidate (candidate: a GNOME Wayland session if locally present; do not assume it is installed or available), the client coverage to test (native Wayland fixture or Chrome, plus XWayland), the setup the user accepts (portal dialogs), and what the probe will not do.
- **Depends on**: [P0.6](P0.6.md).
- **Files**: `docs/evidence/pw0-wayland-candidate.md`.
- **Steps**: inspect local session availability without changing setup, propose exact coverage, then record the existing or newly supplied D-PLATFORM-S decision and its source in the report. No consent-file prerequisite.
- **Contracts/errors**: none.
- **Acceptance**: `bun run validate --case probe.wayland.scope` reports whether D-PLATFORM-S is resolved; an unresolved choice returns `missing` with the question and blocks only dependent probes.
- **Artifacts**: none.
- **Out of scope / escalate**: compositor extensions, brokers, and any deeper compositor integration need separate approval.
