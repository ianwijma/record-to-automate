# probes

[Delivery index](../../DELIVERY-PLAN.md) · [Implementation plan](../../IMPLEMENTATION-PLAN.md) · [Coverage](../coverage.md)

These packets give evidence to later decisions. Their failure or delay blocks nothing on X11. They never install software, change system configuration or use accounts outside the session’s existing authorization. New permissions/accounts/live targets require explicit scope; bookkeeping is done by the executor, not the user.

## Tasks

- [PW.0: Wayland candidate proposal for D-PLATFORM-S](../tasks/PW.0.md)
- [PW.1: Wayland interface inventory](../tasks/PW.1.md)
- [PW.2: Attended Wayland pixels and dispatch probe](../tasks/PW.2.md)
- [PP.1: Provider CLI inventory without account use](../tasks/PP.1.md)
- [PP.2: Live CLI profile probe](../tasks/PP.2.md)
- [PP.3: Jev fixture probe](../tasks/PP.3.md)
