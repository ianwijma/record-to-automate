# PW.1 Wayland interface inventory

[Delivery index](../../DELIVERY-PLAN.md) · [Milestone probes](../milestones/probes.md) · [Implementation plan](../../IMPLEMENTATION-PLAN.md) · [Requirement and W-package mapping](../coverage.md)

Read [execution rules](../execution.md) and [safety invariants](../safety.md) before executing. Consult [experimental defaults](../defaults.md) for implementation constants and [validation contract](../validation.md) when implementing or running acceptance cases. Follow linked dependencies for their contracts; execute only this packet.

- **Outcome**: a report of the session type, compositor, XWayland presence, and the portal interfaces and versions that are actually exposed (ScreenCast, RemoteDesktop, GlobalShortcuts, InputCapture).
- **Depends on**: [PW.0](PW.0.md), D-PLATFORM-S.
- **Files**: `validation/cases/probe-wayland/inventory.ts`.
- **Steps**: inside a Wayland session the user starts, run `gdbus introspect` on `org.freedesktop.portal.Desktop`. Record the `version` properties and environment variables. No permission requests are made.
- **Contracts/errors**: returns `missing` outside a Wayland session.
- **Acceptance**: `bun run validate --suite probe-wayland --case probe.wayland.inventory` (measure).
- **Artifacts**: `inventory.json`.
- **Out of scope / escalate**: interpreting a version label as a capability.
