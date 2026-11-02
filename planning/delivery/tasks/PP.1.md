# PP.1 Provider CLI inventory without account use

[Delivery index](../../DELIVERY-PLAN.md) · [Milestone probes](../milestones/probes.md) · [Implementation plan](../../IMPLEMENTATION-PLAN.md) · [Requirement and W-package mapping](../coverage.md)

Read [execution rules](../execution.md) and [safety invariants](../safety.md) before executing. Consult [experimental defaults](../defaults.md) for implementation constants and [validation contract](../validation.md) when implementing or running acceptance cases. Follow linked dependencies for their contracts; execute only this packet.

- **Outcome**: a report of installed `claude` and `codex` paths and versions, and their `--help` output for the flags the implementation plan names. No prompt is sent and no login state is read.
- **Depends on**: [P0.6](P0.6.md).
- **Files**: `validation/cases/probe-providers/inventory.ts`.
- **Steps**: run `<cli> --version` and `<cli> --help` with an empty, allowlisted environment and a fresh empty home directory passed only in the child environment; never copy the real home or credential files.
- **Contracts/errors**: a missing binary returns `missing`.
- **Acceptance**: `bun run validate --case probe.providers.inventory` (measure).
- **Artifacts**: `providers-inventory.json`.
- **Out of scope / escalate**: any invocation that could use an account.
