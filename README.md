# Home Network Playbook: Maximizing Home Infrastructure

A replicable, documented best practice for getting the most out of the wiring already in a typical house: structured Cat6 runs, RG6 coax in every room, and a central communications panel.

The repo is organized in two phases:

| Phase | Goal | Doc |
|---|---|---|
| 1 | Document the network **as found**: schematics plus written detail so anyone can replicate it | [docs/01-as-found.md](docs/01-as-found.md) |
| 2 | Document **improvement opportunities**, prioritized, with steps, parts and rollback | [docs/02-improvements.md](docs/02-improvements.md) |

Supporting material:

- [docs/03-inventory-template.md](docs/03-inventory-template.md): fill-in sheet to capture your own house the same way
- [docs/04-verification.md](docs/04-verification.md): how to test each link (iperf3, MoCA status, continuity)
- [docs/05-glossary.md](docs/05-glossary.md): terms (ONT, MoCA, POTS, POE filter, double NAT...)
- [sheets/pitch-sheet.pdf](sheets/pitch-sheet.pdf): one-page summary for sharing

## The house in one paragraph

AT&T Fiber enters through an exterior wall to an AT&T device (ONT), then Cat6 to the home comm panel. The home has **Cat6e to every room** (currently terminated at POTS-style jacks) and **RG6 coax to every room on one or more walls**; everything terminates at the comm panel. Today the network is carried to the extra rooms over **MoCA (Ethernet over the coax)** to Nest mesh points, even though home-run Cat6 sits in the same walls. Phase 2 explores using that Cat6 properly.

## Conventions

- Diagrams are [Mermaid](https://mermaid.js.org/) and render natively on GitHub.
- Device model numbers are marked `TBD` where not yet recorded. Fill them in via the [inventory template](docs/03-inventory-template.md).
- "As found" means exactly that: nothing in Phase 1 describes a change. Changes live in Phase 2.

## Status

- [x] Phase 1 as-found documented (October 2026)
- [x] Phase 2 improvement opportunities drafted
- [ ] Record device models, firmware, and measured speeds (see verification doc)
- [ ] Add photos of the comm panel (label first)
- [x] License: GPL v3 (see LICENSE)
