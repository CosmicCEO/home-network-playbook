# Phase 2: Improvement Opportunities

Prioritized from highest value per effort. Each item states the problem, the fix, parts, steps, verification and rollback. Nothing here has been applied; this is the plan.

## Summary

| ID | Opportunity | Effort | Cost (rough) | Impact |
|---|---|---|---|---|
| P2-1 | Re-terminate room-end Cat6e from POTS plugs to RJ45 keystones | Medium | Low | **Very high** |
| P2-2 | Move the network core to the comm panel (patch panel + switch) | Medium | Medium | **Very high** |
| P2-3 | Make the coax plant MoCA-clean (splitter rating, POE filter, terminators) | Low | Low | High |
| P2-4 | Remove double NAT (AT&T gateway passthrough or bridge) | Low | Free | High |
| P2-5 | Use wired backhaul for mesh points (Cat6 first, MoCA as fallback) | Low | Free | High |
| P2-6 | Label, document and back up configs | Low | Low | Medium |
| P2-7 | Add UPS for ONT, gateway, switch | Low | Medium | Medium |
| P2-8 | Optional: upgrade MoCA generation or add access points on PoE | Medium | Medium | Situational |

## P2-1: Re-terminate room-end Cat6e to RJ45

**Problem.** Each room's Cat6e is cut down to a POTS plug. POTS terminations use only 1 to 2 pairs of the 4 pairs (2 to 4 conductors), so gigabit Ethernet cannot run over them as terminated. The cable itself is fine.

**Fix.** Replace each POTS jack with an RJ45 keystone jack, terminated 568B (pick one standard and use it at both ends).

**Parts.** Cat6 keystone jacks, single-gang low-voltage wall plates (or replace the existing plate), punch-down tool, cable stripper.

**Steps.**
1. Label both ends of every run before touching anything (see P2-6).
2. At the room end: remove the POTS jack, check that all four pairs are present and not cut short, re-terminate on a Cat6 keystone, snap it into the plate.
3. At the panel end: confirm each run lands on a numbered punch-down or patch panel port; re-terminate if it lands on a telephone block.
4. Test every run with a cable tester (wire map, then a certifier or at least iperf3 end to end).

**Check.** Wire map passes all 8 conductors, length is below 90 m (295 ft) of permanent link, iperf3 gets about 940 Mbps on gigabit.

**Rollback.** Keep the old plugs; re-terminating is reversible.

## P2-2: Make the comm panel the network core

**Problem.** The router, hub and switch sit in the media room, so every other room reaches the core through a coax detour even though every Cat6e run converges at the panel.

**Fix.** Install a Cat6 patch panel (or keystone punch-down) and a small managed or unmanaged gigabit (or 2.5 GbE) switch in or next to the comm panel. Patch each room's Cat6e to a switch port. Relocate the gateway/ONT patch here as well.

**Parts.** Patch panel or keystone bar, short Cat6 patch cables, switch (PoE-capable if adding access points), shelf or in-panel mount, power outlet in the panel (or a UPS shelf).

**Steps.**
1. Choose the switch location; ensure power and ventilation.
2. Patch ONT Cat6 to the gateway WAN port at the panel (move the gateway if the panel is suitable, or keep it in the media room and patch that run to the panel switch).
3. Patch one switch port per room; confirm link lights.
4. Move wired devices to the room jacks; retire the MoCA hop room by room.
5. Keep the MoCA network up until each room is proven on Cat6.

**Check.** Each room negotiates 1 Gbps full duplex; latency to the gateway under 1 ms inside the LAN.

**Rollback.** Re-plug the old MoCA adapters; nothing is cut.

## P2-3: Make the coax plant MoCA-clean

Even if Cat6 becomes primary, coax can serve rooms that are far from a Cat6 outlet, or as a backup. Keep it healthy.

- **Splitter rating.** Use a splitter rated for 5 to 1675 MHz (MoCA 2.x compatible). Non-rated splitters add loss and kill the MoCA band.
- **Terminate unused legs.** Cap every unused RG6 leg and splitter output with a 75-ohm terminator to reduce reflections.
- **Isolation.** With no cable TV in the house, a POE (point-of-entry) filter is only needed if any outside coax could re-enter; add one if the building ever has a coax drop.
- **Connectors.** Replace corroded or loose F-connectors; compression connectors are preferred.
- **Channel plan.** Set the Actiontec devices to one fixed MoCA channel rather than auto if the network is unstable.

**Check.** MoCA status page on each adapter: link rate, SNR, and PHY rate between nodes.

## P2-4: Remove double NAT

**Problem.** AT&T gateway feeding a Nest router creates two routers in a row (double NAT), which can break port forwarding, hamper gaming and VPN, and complicate troubleshooting.

**Fix, preferred.** Put the AT&T gateway in **IP Passthrough** (to the Nest/primary router) and let the Nest router own DHCP and routing. Turn off Wi-Fi on the AT&T gateway.

**Steps.**
1. Record current AT&T gateway settings (screenshot).
2. Enable IP Passthrough mode for the Nest hub's WAN MAC.
3. Reboot the Nest hub; confirm it gets a public IP.
4. Disable gateway Wi-Fi.

**Check.** Nest hub WAN shows the public IP; online double-NAT tests pass.

**Rollback.** Disable passthrough and reboot both devices.

## P2-5: Wired backhaul for the mesh

**Problem.** If the Nest points cannot see a wired uplink, they fall back to wireless backhaul and cut throughput by half or more per hop.

**Fix.** With Cat6 live in each room (P2-1 and P2-2), plug each Nest point into its room jack so it backhauls over Ethernet. Verify the model supports wired backhaul.

**Check.** The Google Home app shows the point as wired; speed test at the point is close to the wired line rate.

## P2-6: Label, document, back up

- Number every run (C-01, R-01...) on both ends with printed labels.
- Photograph the panel before and after changes.
- Export device configs (Nest, TP-Link, Actiontec) and store them privately; do not commit passwords.
- Fill in the [inventory template](03-inventory-template.md).

## P2-7: Power protection

Put the ONT, gateway and core switch on a UPS (a few hundred VA is enough for these loads). Check ONT battery arrangements with the provider.

## P2-8: Optional

- **MoCA 2.5 adapters** if any rooms must remain on coax and need more than about 1 Gbps.
- **Dedicated Wi-Fi access points on PoE** in place of mesh points, ceiling or wall mounted, using the Cat6 runs and a PoE switch.
- **VLANs** for IoT/guest, if moving to a managed switch and a router that supports them.

## Suggested order of work

1. P2-6 label and photograph
2. P2-3 coax cleanup (cheap, safe)
3. P2-4 remove double NAT
4. P2-1 and P2-2 re-terminate and build the panel core, one room at a time
5. P2-5 wired backhaul
6. P2-7 UPS
7. P2-8 as needed

## Open questions to settle before building

- Cat6e run lengths (all under 90 m?)
- Are the Cat6e runs terminated at the panel on a punch-down or on a phone block?
- Model numbers for Nest points (wired backhaul support) and Actiontec adapters (MoCA 2.0 vs 2.5)
- Does AT&T gateway offer IP Passthrough on this model?
