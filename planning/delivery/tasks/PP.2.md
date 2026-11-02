# PP.2 Live CLI profile probe

[Delivery index](../../DELIVERY-PLAN.md) · [Milestone probes](../milestones/probes.md) · [Implementation plan](../../IMPLEMENTATION-PLAN.md) · [Requirement and W-package mapping](../coverage.md)

Read [execution rules](../execution.md) and [safety invariants](../safety.md) before executing. Consult [experimental defaults](../defaults.md) for implementation constants and [validation contract](../validation.md) when implementing or running acceptance cases. Follow linked dependencies for their contracts; execute only this packet.

- **Outcome**: G-A-CLI cells for one tiny structured prompt per CLI, run under the user's own login with a sanitized environment. Cells cover the reported authentication source, sentinel hooks and MCP, image delivery and cancellation.
- **Depends on**: [PP.1](PP.1.md); explicit authorization for this account-using probe, reused if already given. Production D-AI scope is a later independent decision, not this probe’s prerequisite.
- **Files**: `probes/providers/cli-profile.ts`, `validation/cases/probe-providers/cli-live.ts`.
- **Steps**: follow [implementation plan §9](../../IMPLEMENTATION-PLAN.md#9-optional-ai-and-settings). Show the expected account usage before running.
- **Contracts/errors**: never read credential files, and never use API-key mode as a fallback.
- **Acceptance**: `bun run validate --case probe.providers.cli-live --attended`.
- **Artifacts**: `docs/evidence/pp2-cli.md`.
- **Out of scope / escalate**: product adapters ([P9.2](P9.2.md)).
