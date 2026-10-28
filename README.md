# Home Network Playbook: Maximizing Home Infrastructure

A replicable, documented best practice for getting the most out of the wiring already in a typical house: structured Cat6 runs, RG6 coax in every room, and a central communications panel.

The repo is organized in two phases:

| Phase | Goal | Doc |
|---|---|---|
| 1 | Document the network **as found**: schematics plus written detail so anyone can replicate it | [docs/01-as-found.md](docs/01-as-found.md) |
| 2 | Document **improvement opportunities**, prioritized, with steps, parts and rollback | [docs/02-improvements.md](docs/02-improvements.md) |

Supporting material:

- [docs/03-inventory-template.md](docs/03-inventory-template.md): inventory pre-filled for this house; reusable as a template
- [docs/data/patch-panel.csv](docs/data/patch-panel.csv): patch panel port map
- [docs/04-verification.md](docs/04-verification.md): how to test each link (iperf3, MoCA status, continuity)
- [docs/05-glossary.md](docs/05-glossary.md): terms (ONT, MoCA, POTS, POE filter, double NAT...)
- [sheets/pitch-sheet.pdf](sheets/pitch-sheet.pdf): one-page summary for sharing

## The house in one paragraph

AT&T Fiber enters through an exterior wall to an AT&T device (ONT), then Cat6 to the garage comm panel and on to an AT&T BGW210 gateway. Behind it sit a Google Wifi mesh (four identical AC-1304 units) and a TP-Link switch. The house has **Cat6e to every room**, landed on a 12-port keystone patch panel (RJ45 at the panel; only two rooms are RJ45 at the room end so far) and **RG6 coax to every room**, of which four runs are active. Remote rooms are reached over **MoCA 2.0 (Actiontec ECB6200) through a GE 4-way splitter**, and the mesh nodes backhaul over that MoCA link. Phase 2 plans use of the Cat6 properly, remove double NAT, and identify the five unlabeled patch ports.

## Conventions

- Diagrams are [Mermaid](https://mermaid.js.org/) and render natively on GitHub.
- Device model numbers are marked `TBD` where not yet recorded. Fill them in via the [inventory template](docs/03-inventory-template.md).
- "As found" means exactly that: nothing in Phase 1 describes a change. Changes live in Phase 2.

## Status

- [x] Phase 1 as-found documented (October 2026)
- [x] Phase 2 improvement opportunities drafted
- [x] Core device models recorded (BGW210, Google Wifi AC-1304, ECB6200, GE 4-way splitter)
- [ ] Record measured speeds (see verification doc)
- [ ] Identify the 5 unknown patch-panel ports (P2-0)
- [ ] Add photos of the comm panel (label first)
- [x] License: GPL v3 (see LICENSE)
