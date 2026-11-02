# Precedence and how to use this plan

[Delivery index](../DELIVERY-PLAN.md) · [Implementation plan](../IMPLEMENTATION-PLAN.md)


- [Product decisions](../DECISIONS.md) govern scope. The [design document](../design/DESIGN.md) and [visual journey](../design/journey.html) govern appearance and editor organization. [The implementation plan](../IMPLEMENTATION-PLAN.md) [shared §1](safety.md)–9 and [shared §11](coverage.md)–12 remain the architecture proposals and release qualification reference.
- This plan sets **implementation order and validation** for development previews and replaces [shared §10](decisions.md)'s delivery order for that purpose. The W prerequisites in [shared §10](decisions.md) still apply before a W package is **qualified** or a release claim is made. They do not apply to building or running a preview. Section 11 below maps requirements R01–R20 to preview packets and release W packages.
- The current instruction authorizes the bounded fixture-first experiment and routine reversible implementation work; it does not approve every draft choice in [shared §2](defaults.md). Existing authorization persists. Ask only for genuinely new privileges, account use, live-target scope or unresolved product/release choices at their point of need ([shared §10](decisions.md)). Do not require user-written consent files or another general W00 approval.
- First run: [P0.0](tasks/P0.0.md)–[P0.6](tasks/P0.6.md), a fixed-click CLI loop on a private Xvfb fixture. Start the Electron shell and simple save/reload next; add the existing matcher candidate and recording incrementally. [P0.7](tasks/P0.7.md) observation is needed for recording, not fixed replay. Later packets are contracts where known and bounded contract-writing tasks where evidence is missing. Packet numbers are stable identifiers, not mandatory execution order.

### Packet format

Every packet has the same fields. An executor implements exactly one packet at a time.

- **Outcome**: what exists and runs when the packet is done.
- **Depends on**: packets and decisions that must be complete first. A decision listed here blocks only this packet.
- **Files**: the proposed implementation paths. Every packet also owns its named acceptance-case modules under `validation/cases/`, attended scripts under `validation/manual/`, and the corresponding registry/milestone entries. Expand globs to exact paths in the packet report. Other files need a one-line reason. Update dependent contracts for routine details; escalate scope, safety or user-owned choices.
- **Steps**: the implementation order.
- **Contracts/errors**: interfaces, invariants and typed errors that later packets rely on.
- **Acceptance**: the `bun run validate` selection and the cases that must pass.
- **Artifacts**: what validation writes under `artifacts/validate/<runId>/<caseId>/`.
- **Out of scope / escalate**: what not to build, and when to stop and ask instead of guessing.

### Executor loop

Read this precedence section, [shared §1](safety.md)–3, the selected packet and its direct dependencies. Implement its smallest runnable result, run the named case, fix one concrete failure and rerun that case, then run `bun run validate`. Do not widen research while a failing assertion has a local fix. Report changed paths, exact commands, statuses, evidence paths and remaining decisions. Never mark a packet complete from compilation alone. Register the packet’s cases before implementing its behavior, so missing behavior cannot silently disappear from validation.

### Universal escalation triggers

Stop and report, without working around the problem, when any of these happens:

1. A safety invariant in [shared §1](safety.md) cannot be met as written.
2. A case could pass only by weakening its assertion, mocking native input or timing, or adding a limitation label.
3. Progress needs privileges, an OS permission, account usage or live-target actions outside the session’s existing authorization. Identify the exact new action; an existing authorization needs no renewed approval.
4. An unresolved product choice in [shared §10](decisions.md) is needed. Resolve routine implementation details locally; report a material contract conflict with a concrete proposed correction.
5. A timebox runs out. Report the result as *inconclusive* with evidence and next options. Running out of time does not prove that something is impossible.

