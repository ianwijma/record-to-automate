# Opus responses v2

Author: Claude Opus 5.5 high, 2 November 2026. Covers the findings received in `reviews/astra-on-opus-v1.md` (AO-01 to AO-11), the improvements prompted by my own review of the peer plan (`reviews/opus-on-astra-v1.md`), and the parent review cautions. The revised plan is `opus/plan-v2.md`. I did not read the peer's plan-v2.

**Status vocabulary.** *Plan corrected* means the contract, dependency or gate is now in plan-v2. *Runtime verified* means a gate has produced evidence. **No property in this document is runtime verified.** Every correction below is a plan correction, and its proof is the named gate.

## 1. Findings received

| ID | Disposition | plan-v2 location | Correction made | Remaining proof / user gate |
| --- | --- | --- | --- | --- |
| AO-01 High: Wayland cursor metadata is not event-time position | **Accepted** | §9.2 G-W2 and candidates; §4 privacy; §11 WP3/WP18; §12 | G-W2 now requires, per event, post-acceleration compositor-logical coordinates at event time, the recipient surface and a timestamp in a known clock, for native Wayland and XWayland. Indeterminate events are rejected, never interpolated, and loss is counted. W-b (evdev plus cursor metadata) is demoted to cross-check, citing PipeWire buffer-metadata docs. W-a is limited to partial XWayland coverage. W-c's ability to see client-delivered events is marked unproven. WP18 depends on WP3 *passing*. Escalation alone does not satisfy release, which stays blocked until D-WAY is resolved with evidence | G-W2 evidence; D-WAY; D-PRIV if the broker is needed |
| AO-02 High: abort not isolated; helper death promises | **Accepted** | §1.2; §2.1 (guard split from capture/vision); §2.2 contract and failure table; §3.1 preflight; G-X5/G-W5 | Capture, vision and storage moved out of the guard. Abort runs on a non-blocking thread with a bounded, revocable permit queue, and the in-app abort bypasses the engine. Pause and abort release only ledger inputs. Engine death is handled by the watchdog. Guard SIGKILL is stated as uncleanable: two *candidates* (backend disconnect release; a supervisor fresh-connection release that must first prove it can tell guard-owned from physical inputs), both unproven. Failure is labelled as a limitation and escalated. The tray/notification fallback is removed as proof of abort. Preflight refuses a run without a working abort route | Stall matrix and SIGKILL tests per backend; D-WAY or release-scope decision if a residual remains |
| AO-03 High: freshness deadline underived | **Accepted** | §2.3 `Observation`, `Relied`, `DispatchPermit`; §2.4; §2.5; §3.1; WP7 acceptance | Capture-instant semantics, uncertainty and epochs (suspend, X timestamp wrap, renegotiation) are defined. The deadline is the minimum over all relied-on evidence of capture − uncertainty + maxAge. Refresh needs a journaled confirm observation, and semantic evidence refreshes only through re-verified declared context regions. The guard checks expiry, source epoch, recipient and single use after the durable write and dequeue. Late CLI/Jev results keep their original observation identity. WP7 tests delayed fsync/queue, focus theft, source replacement and duplicates | WP7/WP11 tests; per-role `maxAgeNs` under D-NUM; recipient evidence on Wayland depends on G-W2/W3 |
| AO-04 High: printable keys inferred as text | **Accepted** | §2.3 `KeyEvent`; §4 Keyboard and Privacy; G-X3/G-W3 text cells; WP1, WP10, WP13 | Raw platform-tagged codes, edges, modifiers, layout and capability flags are kept. Extraction yields only physical-key actions. `text-insert` is explicitly authored, either typed in the editor or a recorder suggestion the user confirms, with uncertainty shown and raw events kept. A layout mismatch on replay blocks unless the user approves a remap. Text entry is gated per backend. Keyboard recording without event-time focus evidence stays disabled (new gate D-KEYCAP) | G-X2/G-X3/G-W3 layout, AltGr, Compose and IME cells; D-KEYCAP |
| AO-05 High: automatic fixed fallback | **Accepted** | §4 Default target; §5.2; §12 row "moved targets" | One selected locator per target. Recorded coordinates are evidence only. A user-authored chain advances only on `absent` where the user marked it; ambiguous, unavailable and stale go to the failure policy. A fixed link in a chain needs its own precondition check. The trace records the authorizing locator. A test requires zero dispatch when an image vanishes over a different control | WP11 golden trace; WP13 chain editor |
| AO-06 High: durability and output retries | **Accepted**; the remedy of reducing modes is adopted as a recommendation, not a decision | §2.5 save ordering, history reconciliation, FS probe, power-loss caveat; §6.4 table; WP6, WP16 | The save order covers media, directories, revision and manifest, plus history reconciliation. A filesystem probe was added. Crash consistency is now distinguished from power-loss durability. The recommended initial output is create-new only, with an attempt key, no-replace publishing, directory sync, `RESOLVE_BENEATH` containment, outcome-unknown reconciliation, refusal on unsupported filesystems, and the rule that undo does not undo files. Overwrite and append wait for D-FILE | Fault injection at every boundary; VM hard-reset evidence before any power-loss claim; D-FILE, D-STORE |
| AO-07 High: debug coverage and storage failure | **Accepted** | §10 coverage table; §2.5 storage failure; §3.2 `OutcomeUnknown`; WP17 | Per-workflow toggle recorded in the run header. The coverage table spans actions, checks (true/false/unknown), locator polls, branches, values, output writes, recovery, pause, abort, `OutcomeUnknown`, gaps and provider calls, in both debug and basic modes. The pre-frame is durable before dispatch. Result states are rejected, acknowledged (not verified) and outcome-unknown. Storage failure after dispatch releases, stops and never retries. Logging is explicitly **not** guaranteed when all storage fails. Text-only continuation is a D-RETAIN option | WP17 disk-full/EIO tests before and after dispatch; abrupt-exit and zero-guard-message tests; D-RETAIN |
| AO-08 High: CLI configuration isolation | **Accepted** | §8.2 profiles table and G-AI1; §8.1 Settings; WP19 | Version-pinned profiles with flags verified in current docs: Claude `--safe-mode` keeps authentication and still applies managed policy; `--strict-mcp-config`, `--tools` and `--disallowedTools` included; `--bare` excluded. Codex `--ignore-user-config` keeps `CODEX_HOME` auth, but no documented `exec` flag disables hooks or MCP, so an audit is required. G-AI1 sentinel acceptance as the review requested. Managed policy is disclosed, never bypassed. An adapter whose profile fails is not released for that version | G-AI1 on the user's installed versions; D-AIELIG |
| AO-09 High: Jev uniqueness and semantic refresh | **Accepted**, with my own remedy (declared context regions) | §2.3 `Relied.contextRegions`; §2.4 refresh; §8.3; WP20 | Presence, uniqueness, judgment and Box are bound to one observation. A click also requires §5 and §2.4. Semantic judgments refresh only by re-verifying declared context regions in a journaled confirm observation; otherwise they are re-evaluated or the failure policy applies. Errors yield unavailable/unknown. WP20 tests repeated targets, changed context with an unchanged box, delay and errors | WP20; measurement on animated screens before click capability (risk 4) |
| AO-10 Medium: output identity in re-record | **Accepted** | §2.3 `Output`/`ValueRef`; §6.1 recovery-continue availability; §6.3; §3.2 counters; WP15 | Output IDs and types are separate from step IDs. Output mapping is confirmed per output by the user. A type change gets a new `outputId`. Unresolved outputs leave invalid consumers. Commits are atomic. Recovery-continue paths are included in definite availability. Counters are per occurrence and per run, journaled, restored on resume and never reset by retry-step. No back-edges | WP15 tests: rename, same-type rebind, incompatible replacement, branch-only output, undo |
| AO-11 High: release DAG, Settings, gate wiring | **Accepted**; OpenRouter stays user-owned | §11 (WP19 Settings and CLI, WP20 Jev, WP21 conditional OpenRouter, Slice B and WP22 depend on them); §12 closure table; §13 "Before" column | WP19/WP20 sit in the release closure through Slice B. WP19 delivers Settings with the full error test list. The OpenRouter stub was dropped; WP21 exists only if D-OR selects it. Each D-gate names the package it blocks. WP18 needs passing Wayland gates. A requirement-to-test closure table replaces the blanket sentence | Closure rows are proven only at WP22 on both sessions |

## 2. Improvements from my own critique of the peer plan

These are weaknesses of my v1 that I acknowledged while reviewing Astra.

| Self-finding | plan-v2 location | Change |
| --- | --- | --- |
| Key/text capture was wrong ("printable keys → type text") | §4 | Replaced; see AO-04 |
| Unsafe fixed fallback | §4, §5.2 | Removed; see AO-05 |
| Overwrite/append modes lacked retry and `OutcomeUnknown` semantics | §6.4 | Create-new recommended; other modes deferred to D-FILE |
| Rust binding-maturity, "no Node bindings" and N-API ABI claims unproven | §2.1, §9.3 | Withdrawn. G-H0 compares Rust and C++ (and C for the guard) on probes. The rebuild cost is cited from the Electron doc only |
| Ubuntu 26.04 compositor suggestion unverified | §9.2 G-W0, D-WAY | No distribution version asserted; introspection decides |
| My review cited a forum for GNOME's GlobalShortcuts version (OA-03) | §9.2 G-W0 | Replaced with primary evidence: xdg-desktop-portal-gnome NEWS lists "Add global shortcuts portal backend" first at 48.rc. Availability still counts only if introspected |
| Sampler fallback to the pole of inaccessibility was ad hoc | §5.1 | Exact enumeration of the finite eligible set (adopted from Astra) |
| Persistence ordering versus deadline (my OA-04 against Astra applied to my own plan) | §2.5 | Intent written in every mode; guard checks after the write |
| Missing launch/attach package (my OA-11 against Astra; my v1 had it only as a spike) | §3.3, WP12 | Added to Slice A |
| Broker security and per-compositor abort route (my OA-01/OA-03) | §9.2 broker contract, G-W5 | Applied to my own W-b/broker path |
| Arbitrary "two failures = failed" stop rule | §9.1 | Per-cell setup allowance, then outcome categories |
| "Zero wrong targets on 200 frames" read as accuracy | §7 | Experimental size; finite-sample bound stated; user-approved adequacy |

## 3. Cross-plan contributions

**Adopted from Astra:**

- the single-use permit bound to observation, epoch, deadline and geometry;
- the four-state `LocateResult`;
- the honest key model;
- create-new outputs with no-replace publishing and reconciliation;
- the delayed-judgment stance, refined with context regions;
- the requirement that preflight checks a working abort route;
- per-capability investigation discipline;
- the scanline sampler;
- the Electron security posture;
- the conservative note that Jev on animated screens may fail.

**Retained from mine for synthesis:**

- the granular work-package DAG;
- the fixture timecode and received-event ledger;
- the lossless ring buffer;
- XI2 `sourceid` separation;
- the XWayland RECORD candidate (labelled partial);
- the physical-input abort signal;
- re-record steps that keep their IDs (now with separate output identity);
- recovery exit semantics and counters;
- `unshare -n` CI;
- restore-token and prompt measurement;
- `r2a doctor`;
- the Runs viewer with no IPC route to the guard;
- the requirement closure table.

**Not adopted:**

- **A forced switch to C++.** Astra's review itself calls the language a preference; G-H0 decides.
- **Astra's D6 experimental numbers** (10 s, 150 ms and so on). I neither adopt nor reject them; D-NUM owns them.
- **Astra's recommendation of a GNOME extension plus broker as the D4 default.** Kept as candidate W-c plus an optional broker, not a recommendation.
- **The fixed two-plus-two-day budget.** Replaced by per-cell setup allowances.

## 4. Parent-caution compliance

| Caution | Where addressed |
| --- | --- |
| Forum-only and general claims | §9.2 G-W0 (primary NEWS); §2.1 claims withdrawn; XWayland and Proton statements turned into probes (§3.3, W-a) |
| Killed guard; supervisor only a candidate; queue isolation | §2.2 |
| Deadline from capture and all relied-on evidence; journaled refresh; late identity; no automatic retry on `OutcomeUnknown` | §2.4, §2.5, §3.2 |
| Physical keys are not text; no recognition fallback | §4, §5.2 |
| Wayland event-time recipient and coordinates; focus filtering; broker contract | §9.2, §4 |
| Numbers are experimental; finite sample; per-capability stops | §7, §9.1 |
| Copy and re-record identities; recovery counters | §6.2, §6.3, §3.2 |
| Durability scope; output attempt identity; undo | §2.5, §6.4 |
| Debug toggle and coverage; logging not guaranteed | §10, §2.5 |
| CLI profiles, auth, managed policy, no billing substitution; Jev required; Settings in closure | §8, §11, §12 |
| Preferred stack, spacious UX, no new scope | §2.1, §12 UX row. No linked copies, loops, scripts, all-application key logging or broad compositor promises |

## 5. Remaining High/Blocker issues in plan-v2

These are open plan risks, not unaddressed findings.

1. **High, feasibility: G-W2.** No candidate mechanism is known to deliver event-time recipient and coordinates on Wayland. The release is blocked until it passes or the user changes D-WAY.
2. **High, feasibility: guard-death release.** No backend has evidence that held inputs are released when the guard is killed. A residual limitation may need user acceptance.
3. **High, user decision: D-KEYCAP and Wayland recipient evidence.** If event-time focus cannot be proven, keyboard recording or the recipient check is disabled or limited on that backend, and that needs a user decision.

No Blocker remains in the plan text.

## 6. Merge recommendations

1. **Adopt one safety core verbatim.** Use Astra's permit and `LocateResult` shapes plus this plan's `Relied` and deadline derivation, journaled refresh, context regions and guard-death candidate table. *Tradeoff:* conservative refresh may reduce Jev's usefulness on animated screens; measure before approving click capability.
2. **Keep the native language and compositor as evidence gates (G-H0, G-W0/W2), not merged preferences.** *Tradeoff:* starting WP7 waits for the first X11 gate cells and the comparison probe, but this avoids locking in an unproven binding stack. Use provisional spike code meanwhile.
3. **Use this plan's DAG and closure table, with Astra's create-new outputs and key model as the recommended defaults behind D-FILE and D-KEYCAP.** *Tradeoff:* the release scope is larger (Settings, CLI and Jev adapters are mandatory), and append/overwrite users wait for a D-FILE decision. In exchange, no nominally complete release ships without required features.
