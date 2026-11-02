# Requirement coverage

[Delivery index](../DELIVERY-PLAN.md) · [Implementation plan](../IMPLEMENTATION-PLAN.md)


| ID | Requirement | Preview evidence (packets) | Release evidence (W, per implementation plan [shared §11](coverage.md)) |
| --- | --- | --- | --- |
| R01 | X11 and Wayland | [P0.4](tasks/P0.4.md)–[P0.9](tasks/P0.9.md), [P2.8](tasks/P2.8.md); [PW.1](tasks/PW.1.md)–[PW.2](tasks/PW.2.md) | W01/W02/W06/W14 via [P7.1](tasks/P7.1.md), [P8.0](tasks/P8.0.md)–[P8.4](tasks/P8.4.md) |
| R02 | Last Z and general apps | Fixture in M0–M2, [P3.1](tasks/P3.1.md) Chrome, [P3.4](tasks/P3.4.md) Last Z | W09/Slice B via [P10.1](tasks/P10.1.md) |
| R03 | Attach, launch, readiness, invocation | [P0.6](tasks/P0.6.md) attach, [P3.5](tasks/P3.5.md), [P6.4](tasks/P6.4.md), [P2.6](tasks/P2.6.md) | W09/W14/W18 via [P6.4](tasks/P6.4.md), [P10.0](tasks/P10.0.md) |
| R04 | Automatic screen and input recording | [P0.7](tasks/P0.7.md), [P2.3](tasks/P2.3.md), [P4.1](tasks/P4.1.md)–[P4.3](tasks/P4.3.md), [P4.8](tasks/P4.8.md) | W08 |
| R05 | Edit, independent copy, re-record, undo | [P2.5](tasks/P2.5.md) edit, [P5.1](tasks/P5.1.md)–[P5.4](tasks/P5.4.md) | W09a/W10/W11 |
| R06 | Screen branches in one workflow | [P3.3](tasks/P3.3.md) | W09/W10 |
| R07 | Fixed, region, OCR, image and moved targets | [P0.6](tasks/P0.6.md), [P1.1](tasks/P1.1.md)–[P1.3](tasks/P1.3.md), [P3.2](tasks/P3.2.md), [P4.6](tasks/P4.6.md) | W07/W10 |
| R08 | Text, screenshot, value and file outputs | [P3.2](tasks/P3.2.md) (read value), [P6.1](tasks/P6.1.md) | W10/W12 |
| R09 | Offline core | Network-isolated fixture core suites; [P9.1](tasks/P9.1.md) no-spawn (a live target’s own network needs are separate) | W18 via [P10.2](tasks/P10.2.md) |
| R10 | Own-login CLI integration in the UI | [PP.1](tasks/PP.1.md)–[PP.2](tasks/PP.2.md) | W15s/W15 via [P9.1](tasks/P9.1.md)–[P9.2](tasks/P9.2.md) |
| R11 | Optional runtime Jev | [PP.3](tasks/PP.3.md) | W16 via [P9.3](tasks/P9.3.md) |
| R12 | Optional OpenRouter | — | W17 via [P9.4](tasks/P9.4.md) if D-ROUTER selects it |
| R13 | Position-only variation | [P4.4](tasks/P4.4.md) | W07/W10 |
| R14 | Stop by default, pause, recovery | [P0.6](tasks/P0.6.md), [P2.4](tasks/P2.4.md), [P6.3](tasks/P6.3.md) | W09/W13 |
| R15 | Debug screenshot evidence and replay | [P2.4](tasks/P2.4.md) run records, [P6.2](tasks/P6.2.md) | W13 |
| R16 | Approved easy-to-use dark organization | [P2.5](tasks/P2.5.md), [P4.6](tasks/P4.6.md), [P5.4](tasks/P5.4.md), [P6.2](tasks/P6.2.md), [P9.1](tasks/P9.1.md) | W10/W13/W15/W18 via user review |
| R17 | Open-source release | — | W18 via [P10.0](tasks/P10.0.md), [P10.2](tasks/P10.2.md) |
| R18 | Future Windows/macOS | Platform-neutral host protocol and schema ([P0.2](tasks/P0.2.md), [P2.2](tasks/P2.2.md)) | Later adapters; no current claim |
| R19 | Independent abort and owned-input release | [P0.5](tasks/P0.5.md), [P0.8](tasks/P0.8.md), [P0.9](tasks/P0.9.md), [P2.1](tasks/P2.1.md), [P2.6](tasks/P2.6.md), [P2.8](tasks/P2.8.md), [P4.1](tasks/P4.1.md) | W01/W02/W06/W09/W13/W14 via [P7.1](tasks/P7.1.md), [P8.1](tasks/P8.1.md)–[P8.4](tasks/P8.4.md) |
| R20 | No unchecked dispatch or automatic unknown-effect retry | [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P1.3](tasks/P1.3.md), [P2.4](tasks/P2.4.md), [P3.3](tasks/P3.3.md), [P6.1](tasks/P6.1.md) | W01 G-L/W02/W06/W07/W09/W12/W16 |

Confirmed decisions not listed above:

- **Independent copies only**: [P5.1](tasks/P5.1.md).
- **Per-workflow debug**: [P6.2](tasks/P6.2.md). Preview diagnostic artifacts follow [shared §2](defaults.md) and do not settle product retention.
- **Stop and explain by default**: S6 and [P2.4](tasks/P2.4.md).
- **No timing randomization**: [P4.4](tasks/P4.4.md).
- **Recorded actions are editable**: what the user calls "annotation" is [P2.5](tasks/P2.5.md) and [P4.6](tasks/P4.6.md).
- **Polished dark visual direction and editor organization**: R16.

None of the provisional choices in [shared §2](defaults.md) overrides a confirmed decision.


## Implementation work packages to delivery tasks

This lookup follows the requirement coverage rows above. W packages span development and qualification; follow each task’s own prerequisites.

<a id="w01"></a>

### W01

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.4](tasks/P0.4.md), [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P0.7](tasks/P0.7.md), [P0.8](tasks/P0.8.md), [P0.9](tasks/P0.9.md), [P1.3](tasks/P1.3.md), [P2.1](tasks/P2.1.md), [P2.4](tasks/P2.4.md), [P2.6](tasks/P2.6.md), [P2.8](tasks/P2.8.md), [P3.3](tasks/P3.3.md), [P4.1](tasks/P4.1.md), [P6.1](tasks/P6.1.md), [P7.1](tasks/P7.1.md), [P8.0](tasks/P8.0.md), [P8.1](tasks/P8.1.md), [P8.2](tasks/P8.2.md), [P8.3](tasks/P8.3.md), [P8.4](tasks/P8.4.md), [PW.1](tasks/PW.1.md), [PW.2](tasks/PW.2.md)

<a id="w02"></a>

### W02

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.4](tasks/P0.4.md), [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P0.7](tasks/P0.7.md), [P0.8](tasks/P0.8.md), [P0.9](tasks/P0.9.md), [P1.3](tasks/P1.3.md), [P2.1](tasks/P2.1.md), [P2.4](tasks/P2.4.md), [P2.6](tasks/P2.6.md), [P2.8](tasks/P2.8.md), [P3.3](tasks/P3.3.md), [P4.1](tasks/P4.1.md), [P6.1](tasks/P6.1.md), [P7.1](tasks/P7.1.md), [P8.0](tasks/P8.0.md), [P8.1](tasks/P8.1.md), [P8.2](tasks/P8.2.md), [P8.3](tasks/P8.3.md), [P8.4](tasks/P8.4.md), [PW.1](tasks/PW.1.md), [PW.2](tasks/PW.2.md)

<a id="w06"></a>

### W06

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.4](tasks/P0.4.md), [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P0.7](tasks/P0.7.md), [P0.8](tasks/P0.8.md), [P0.9](tasks/P0.9.md), [P1.3](tasks/P1.3.md), [P2.1](tasks/P2.1.md), [P2.4](tasks/P2.4.md), [P2.6](tasks/P2.6.md), [P2.8](tasks/P2.8.md), [P3.3](tasks/P3.3.md), [P4.1](tasks/P4.1.md), [P6.1](tasks/P6.1.md), [P7.1](tasks/P7.1.md), [P8.0](tasks/P8.0.md), [P8.1](tasks/P8.1.md), [P8.2](tasks/P8.2.md), [P8.3](tasks/P8.3.md), [P8.4](tasks/P8.4.md), [PW.1](tasks/PW.1.md), [PW.2](tasks/PW.2.md)

<a id="w07"></a>

### W07

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P1.1](tasks/P1.1.md), [P1.2](tasks/P1.2.md), [P1.3](tasks/P1.3.md), [P2.4](tasks/P2.4.md), [P3.2](tasks/P3.2.md), [P3.3](tasks/P3.3.md), [P4.4](tasks/P4.4.md), [P4.6](tasks/P4.6.md), [P6.1](tasks/P6.1.md)

<a id="w08"></a>

### W08

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.7](tasks/P0.7.md), [P2.3](tasks/P2.3.md), [P4.1](tasks/P4.1.md), [P4.2](tasks/P4.2.md), [P4.3](tasks/P4.3.md), [P4.8](tasks/P4.8.md)

<a id="w09"></a>

### W09

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P0.8](tasks/P0.8.md), [P0.9](tasks/P0.9.md), [P1.3](tasks/P1.3.md), [P10.0](tasks/P10.0.md), [P10.1](tasks/P10.1.md), [P2.1](tasks/P2.1.md), [P2.4](tasks/P2.4.md), [P2.6](tasks/P2.6.md), [P2.8](tasks/P2.8.md), [P3.1](tasks/P3.1.md), [P3.3](tasks/P3.3.md), [P3.4](tasks/P3.4.md), [P3.5](tasks/P3.5.md), [P4.1](tasks/P4.1.md), [P6.1](tasks/P6.1.md), [P6.3](tasks/P6.3.md), [P6.4](tasks/P6.4.md), [P7.1](tasks/P7.1.md), [P8.1](tasks/P8.1.md), [P8.2](tasks/P8.2.md), [P8.3](tasks/P8.3.md), [P8.4](tasks/P8.4.md)

<a id="w09a"></a>

### W09a

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P2.5](tasks/P2.5.md), [P5.1](tasks/P5.1.md), [P5.2](tasks/P5.2.md), [P5.3](tasks/P5.3.md), [P5.4](tasks/P5.4.md)

<a id="w10"></a>

### W10

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.6](tasks/P0.6.md), [P1.1](tasks/P1.1.md), [P1.2](tasks/P1.2.md), [P1.3](tasks/P1.3.md), [P2.5](tasks/P2.5.md), [P3.2](tasks/P3.2.md), [P3.3](tasks/P3.3.md), [P4.4](tasks/P4.4.md), [P4.6](tasks/P4.6.md), [P5.1](tasks/P5.1.md), [P5.2](tasks/P5.2.md), [P5.3](tasks/P5.3.md), [P5.4](tasks/P5.4.md), [P6.1](tasks/P6.1.md), [P6.2](tasks/P6.2.md), [P9.1](tasks/P9.1.md)

<a id="w11"></a>

### W11

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P2.5](tasks/P2.5.md), [P5.1](tasks/P5.1.md), [P5.2](tasks/P5.2.md), [P5.3](tasks/P5.3.md), [P5.4](tasks/P5.4.md)

<a id="w12"></a>

### W12

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P1.3](tasks/P1.3.md), [P2.4](tasks/P2.4.md), [P3.2](tasks/P3.2.md), [P3.3](tasks/P3.3.md), [P6.1](tasks/P6.1.md)

<a id="w13"></a>

### W13

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P0.8](tasks/P0.8.md), [P0.9](tasks/P0.9.md), [P2.1](tasks/P2.1.md), [P2.4](tasks/P2.4.md), [P2.5](tasks/P2.5.md), [P2.6](tasks/P2.6.md), [P2.8](tasks/P2.8.md), [P4.1](tasks/P4.1.md), [P4.6](tasks/P4.6.md), [P5.4](tasks/P5.4.md), [P6.2](tasks/P6.2.md), [P6.3](tasks/P6.3.md), [P7.1](tasks/P7.1.md), [P8.1](tasks/P8.1.md), [P8.2](tasks/P8.2.md), [P8.3](tasks/P8.3.md), [P8.4](tasks/P8.4.md), [P9.1](tasks/P9.1.md)

<a id="w14"></a>

### W14

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.4](tasks/P0.4.md), [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P0.7](tasks/P0.7.md), [P0.8](tasks/P0.8.md), [P0.9](tasks/P0.9.md), [P10.0](tasks/P10.0.md), [P2.1](tasks/P2.1.md), [P2.6](tasks/P2.6.md), [P2.8](tasks/P2.8.md), [P3.5](tasks/P3.5.md), [P4.1](tasks/P4.1.md), [P6.4](tasks/P6.4.md), [P7.1](tasks/P7.1.md), [P8.0](tasks/P8.0.md), [P8.1](tasks/P8.1.md), [P8.2](tasks/P8.2.md), [P8.3](tasks/P8.3.md), [P8.4](tasks/P8.4.md), [PW.1](tasks/PW.1.md), [PW.2](tasks/PW.2.md)

<a id="w15"></a>

### W15

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P2.5](tasks/P2.5.md), [P4.6](tasks/P4.6.md), [P5.4](tasks/P5.4.md), [P6.2](tasks/P6.2.md), [P9.1](tasks/P9.1.md), [P9.2](tasks/P9.2.md), [PP.1](tasks/PP.1.md), [PP.2](tasks/PP.2.md)

<a id="w15s"></a>

### W15s

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P9.1](tasks/P9.1.md), [P9.2](tasks/P9.2.md), [PP.1](tasks/PP.1.md), [PP.2](tasks/PP.2.md)

<a id="w16"></a>

### W16

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.5](tasks/P0.5.md), [P0.6](tasks/P0.6.md), [P1.3](tasks/P1.3.md), [P2.4](tasks/P2.4.md), [P3.3](tasks/P3.3.md), [P6.1](tasks/P6.1.md), [P9.3](tasks/P9.3.md), [PP.3](tasks/PP.3.md)

<a id="w17"></a>

### W17

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P9.4](tasks/P9.4.md)

<a id="w18"></a>

### W18

[Implementation package](../IMPLEMENTATION-PLAN.md#10-work-packages-and-dependency-closure) · [P0.6](tasks/P0.6.md), [P10.0](tasks/P10.0.md), [P10.2](tasks/P10.2.md), [P2.5](tasks/P2.5.md), [P2.6](tasks/P2.6.md), [P3.5](tasks/P3.5.md), [P4.6](tasks/P4.6.md), [P5.4](tasks/P5.4.md), [P6.2](tasks/P6.2.md), [P6.4](tasks/P6.4.md), [P9.1](tasks/P9.1.md)

