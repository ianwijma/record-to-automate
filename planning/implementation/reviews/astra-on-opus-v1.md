# Astra review of Opus plan v1

**Verdict: needs substantial revision.** The architecture is usable, but dispatch safety, recording fidelity, durability, and release dependency closure need explicit contracts before synthesis. Retain the granular work packages, fixture timecode/input ledger, lossless crop buffer, measured defaults, deep-copy remapping, network-disabled acceptance, and Runs viewer with no host IPC. These are strong contributions.

Read DECISIONS.md, PLAN.md and both plans in full; consulted the approved mockup source. Confirmed decisions override proposals and stale mockup copy. No other review was read; no implementation or live tests were performed.

**AO-01 — High — Technical correctness / missing contract. §7 G-W1/G-W2; WP3, WP17.**

W-b combines evdev button events with ScreenCast cursor metadata, but “positions map to frames” does not prove the position at each button edge. Rapid move–click–move can occur between cursor samples. PipeWire describes cursor information as buffer metadata, not a synchronized physical-button event stream ([PipeWire](https://docs.pipewire.org/group__spa__buffer.html)). Require event-time compositor coordinates, timestamps and bounded association uncertainty; reject indeterminate events rather than interpolate silently. Test sub-frame clicks/drags against fixture-received coordinates, including accelerated motion, native Wayland and XWayland. W-a needs explicit coverage limits too. If no candidate proves ordinary passive recording, escalate setup/compositor choices; WP3 escalation alone must not satisfy release.

**AO-02 — High — Requirement mismatch / missing contract. §2.1–2.2, G-X5/G-W5; WP7, WP17.**

The host owns both capture and abort without an independent scheduling/IPC contract. A blocked capture call can defeat its heartbeat watchdog. Tray/notification fallback also depends on usable UI and is not proof of always-available abort. Isolate dispatch/abort from capture, encoding, vision, storage and UI waits; specify cancellation of queued commands and release of owned inputs on pause/abort/lease loss. Test stalls in each subsystem while dragging/holding. Distinguish engine death from host crash/SIGKILL: the killed host cannot run cleanup. Document and test backend disconnect behavior and residual limitations; do not promise universal release after helper death.

**AO-03 — High — Missing contract. §2.2, §3–5, §10; WP7–WP11.**

`notAfterMonotonicNs` has no derivation from capture time and uncertainty. Confirm happens before an unbounded fsync, so a freshly minted deadline could authorize stale pixels. Geometry hashes also do not prove current focus/recipient identity. Define capture timestamp semantics, clock discontinuities, uncertainty, confirmation-observation linkage, and a deadline derived from the oldest evidence still relied upon. Recheck expiry, source epoch and recipient/focus immediately at dispatch after logging; expired requests must reacquire. Make commands single-use. Acceptance: delayed fsync/queue, focus theft, source replacement, timestamp uncertainty and duplicate delivery all prevent stale/duplicate input and appear in the trace.

**AO-04 — High — Technical correctness. §4 semantic extraction; WP10.**

“Printable keys in a row → Type text” confuses physical input with committed text, and can convert game movement keys into typing. Layout changes, modifiers, dead keys and IMEs defeat that inference; even Compose needs state ([xkbcommon](https://xkbcommon.org/doc/current/group__compose.html)). Preserve physical codes, press/release, repeats, holds, layout/modifier context and raw evidence. Distinguish physical-key replay from explicit text insertion; only infer committed text with a demonstrated source and expose uncertainty otherwise. Test gameplay letters, non-US layouts, Compose/dead keys, IME composition and changed replay layouts; unsupported capture must remain visible/editable rather than claim recovered text.

**AO-05 — High — Requirement mismatch. §4 final paragraph, §8 locator stack; WP9–WP11.**

An automatically assigned image target “with a fixed fallback,” combined with locators “tried in order,” leaves permission to click unresolved when recognition fails. That can turn a missing or moved target into an unchecked old-coordinate click. Define one selected locator or an explicitly authored fallback chain; automatically recorded coordinates are evidence, not fallback authorization. Missing, ambiguous, stale or unavailable recognition must reach the configured failure policy. Test a vanished image over a different control: zero dispatch. Separately authored fixed targets remain supported with fresh source/focus/geometry checks.

**AO-06 — High — Technical correctness / missing contract. §2.6, §6 file outputs; WP6, WP15.**

Atomic rename alone is not crash durability. The save sequence omits durable assets, manifest contents, directory entries and history ordering. Linux requires directory fsync separately ([fsync](https://www.man7.org/linux/man-pages/man2/fsync.2.html)). Specify asset/revision persistence before publishing head, fsync the replacement manifest and affected directories, and reconcile interrupted history updates. File modes also lack retry identities: a crash after append/publication but before success logging can duplicate rows/files. Define collision, overwrite, append atomicity, CSV schema/header, symlink containment and outcome-unknown reconciliation, or propose fewer modes to the user. Test interruption at every persistence boundary, including publication-before-acknowledgment; process-kill tests alone do not establish power-loss durability.

**AO-07 — High — Requirement mismatch / missing contract. §10; WP11, WP16.**

Trace kinds do not explicitly guarantee the required pre-frame for every action/check and locator attempt, post-action evidence, or a per-workflow debug toggle. “Always recorded” basic errors cannot be guaranteed after storage failure. Require a coverage table including false conditions, failures, outputs and recovery; persist required pre-evidence before dispatch. Distinguish rejected, dispatched/acknowledged, verified and outcome-unknown. If a write fails after dispatch, stop/release, display incomplete evidence and never automatically retry the uncertain action. Any text-only continuation is an explicit unresolved user policy. Acceptance: disk-full/EIO before and after dispatch, missing frames, abrupt exit, historical revision inspection, and zero-input replay.

**AO-08 — High — Missing contract. §9 CLI adapters; WP18.**

An empty working directory and disclosure of inherited configuration do not constrain hooks/plugins/MCP or prevent assistants acting outside validated edit proposals. Define version-pinned invocation profiles that restrict tools and inherited configuration while retaining CLI-owned subscription authentication; do not copy credentials or silently switch billing. Claude documents `--safe-mode` with normal authentication but managed-hook exceptions, plus separate tool/MCP controls; evaluate those rather than assume disclosure suffices ([CLI reference](https://code.claude.com/docs/en/cli-reference)). Codex likewise exposes hook/plugin/MCP configuration requiring an isolation audit ([configuration](https://learn.chatgpt.com/docs/config-file/config-reference)). Gate acceptance on sentinel hooks/tools remaining inactive, unchanged user settings, eligible login working and late cancelled responses being discarded. Reconsider integration if these cannot coexist.

**AO-09 — High — Missing contract. §3 Confirm, §9 Jev; WP18.**

Presence plus plausible bounds does not establish uniqueness. Comparing only the returned box also cannot refresh a semantic judgment: an unchanged button can remain visible while the state that justified pressing it changes elsewhere. Jev's Box documentation confirms absent targets still return coordinates ([Box](https://docs.typesafe.ai/primitives/box)). Bind presence, uniqueness, judgment, box and transforms to the same observation. Define the relevant semantic context and how fresh evidence revalidates it; reject or repeat stale/ambiguous judgments. Test repeated targets, changed surrounding state with unchanged box pixels, delayed responses and malformed/unsupported-model/provider failures. Errors must remain unknown/failure, never false or click permission.

**AO-10 — Medium — Missing contract. §2.5, §6 re-record; WP14.**

Keeping mapped step IDs does not preserve output binding IDs, types or meaning. A mapped extraction can retain its step ID while changing number→text or dropping one of several outputs. Specify producer/output/type identity separately; require explicit compatible output mapping, retain unresolved references as invalid, and revalidate all consumers atomically. Include recovery-continue paths in definite availability. Acceptance: rename, same-type rebind, incompatible replacement, branch-only output and undo preserve intended bindings without silent reassignment. Bounded recovery counters must survive retry-step rather than reset indefinitely; retain structured blocks without arbitrary graph back-edges.

**AO-11 — High — Sequencing/release gap. §11–12; WP0–WP19.**

WP19 depends on Slice B but not WP18, so optional-at-use CLI assistance/Jev can be absent from a nominally complete release. WP18 also lacks an explicit Settings deliverable. Add UI setup for executable/version, enabled state, auth readiness, model/capabilities, tests and billing; test auth, limits, unavailable models, refusal, transport, malformed output and cancellation. Make release depend on that completed work. Keep OpenRouter priority user-owned; a stub is not delivered support. Wire D-HOST/UI/OCR/SPLIT/KEYS/NUM/RETAIN and conditional D-PRIV before their dependent implementation/defaults, and require successful Wayland gates. Add requirement-to-test closure covering offline core, independent copies, position-only variation, actual screen branches, outputs, debug/replay, attach/launch readiness, Chrome and Last Z on both sessions. The blanket final acceptance sentence does not repair the dependency graph.

**Tradeoffs and synthesis recommendations.**

- Rust is a credible alternative to my C++ proposal. Compare both as out-of-process helpers, validate bindings and packaging, and let measured integration cost decide; language preference is not a blocker. I would adopt Opus's finer work-package structure and lossless ring-buffer detail.
- File-canonical storage is shared ground; my plan also needs explicit directory fsync. SQLite remains a valid tradeoff if transactional complexity wins. My create-new output proposal reduces retry complexity; Opus's richer modes are useful only after their semantics are chosen and tested.
- Opus's broader Wayland candidate search is better than prematurely committing to my GNOME bridge. Neither a frame cursor nor my proposed extension is proven event-time observation. My proposed local relocalization after Jev also needs revision: finding the same target cannot alone refresh its semantic justification.
- Retain structured screen conditions, nonrecursive bounded recovery, independent copies and approved Actions/Flow/Values/Runs organization. No loops, subworkflows or arbitrary graph edges are implied. Keep language, compositor/permissions, output modes, retention, numeric defaults, license/packages and OpenRouter priority with the user.

Revise the contracts and release prerequisites above, then synthesize; do not resolve these by choosing my libraries automatically.

**Counts:** Blocker 0; High 10; Medium 1; Low 0.

**Highest-priority fixes:** AO-01 event-time Wayland proof; AO-02 independent abort and honest crash limits; AO-11 complete release dependency closure.

**Artifact:** `/home/ian/record-to-automate/planning/implementation/reviews/astra-on-opus-v1.md`
