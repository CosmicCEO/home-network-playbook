# Phase 2: Improvement Opportunities

Prioritized from highest value per effort, based on the confirmed as-found configuration. Nothing here has been applied except where marked **Done**.

## Summary

| ID | Opportunity | Status | Effort | Cost (rough) | Impact |
|---|---|---|---|---|---|
| P2-0 | Identify and label the 5 unknown patch-panel ports | To do | Low | Free to low | High (prerequisite) |
| P2-1 | Re-terminate Cat6e from POTS plugs to RJ45 keystones | **Panel end done; room ends done in upstairs middle and basement office**; rest to do | Medium | Low | Very high |
| P2-2 | Move gateway and hub to the garage panel (option C), add a switch, patch rooms to it | Option C chosen; to do | Medium | Medium | **Very high** |
| P2-3 | Finish the coax cleanup (cap IN port, check adapter) | To do | Low | Low | Medium |
| P2-4 | Remove double NAT with BGW210 IP Passthrough | To do | Low | Free | High |
| P2-5 | Move mesh backhaul from MoCA to Cat6 where a run exists | To do (low priority) | Low | Free | Low (MoCA measured 890 Mbps) |
| P2-6 | Label, document, back up configs | Partly done (panel sheet) | Low | Low | Medium |
| P2-7 | UPS for ONT, gateway, switch | To do | Low | Medium | Medium |
| P2-8 | Optional: MoCA 2.5, PoE access points, newer mesh | Optional | Medium | Medium | Situational |

## P2-0: Identify the unknown ports

**Problem.** Five of the 12 patch-panel ports (A3, A5, A6, B3, B4) are labeled UNK, and the labeled ones (Great Room, Master, Upstairs Center, Kitchen, Attic Cable) are unconfirmed as to where they land. Every later step depends on knowing which port serves which room.

**Fix.** Use a tone generator and probe, or a cable tester with remote ends, to trace each port to its room jack. Update [data/patch-panel.csv](data/patch-panel.csv) and the table in the as-found doc.

**Check.** Every port has a room and wall-plate location; every room that has coax also has a known Cat6e port (or is marked as having none).

## P2-1: Cat6e termination (panel done, rooms partly done)

**Status.** The panel end is complete: every run is on an RJ45 keystone on the patch panel. At the **room end**, only **two rooms** (upstairs middle and basement office) have been converted to RJ45, plus the media room run that carries the AT&T uplink; the remaining rooms are still POTS-style plugs, which use only 2 to 4 of the 8 conductors and cannot carry gigabit Ethernet.

**Remaining.**
1. Record which patch ports the two finished rooms land on (open question O-2).
2. Re-terminate each remaining room-end jack from POTS to a Cat6 RJ45 keystone (same wiring standard as the panel, 568B or 568A).
3. Test every run with a wire-map cable tester (all 8 conductors straight through).
4. Record lengths; permanent links must be under 90 m (295 ft).
5. Prioritize the three rooms that use MoCA (top floor back right, sitting room media cabinet, master). None is RJ45 at the room end yet, so moving their traffic to Cat6 is blocked until they are converted. The two finished rooms (upstairs middle, basement office) can use the new switch as soon as P2-2 is done.

**Check.** Wire map passes; an iperf3 test across a patched run reaches about 940 Mbps on gigabit.

## P2-2: Add a switch at the panel

**Problem.** The patch panel exists, but there is no switch at the garage panel and only one patch cable is installed (B1 to B2, carrying the AT&T uplink), so the Cat6e runs cannot carry room traffic. Remote rooms still depend on the shared MoCA 2.0 channel.

**Constraint.** The gateway and hub stay in the media room, and the media room has a **single** Cat6e run (B2), which already carries the AT&T WAN uplink. A panel switch can be added without moving the gateway, but it needs its own LAN path to the hub, and that run is taken.

**Options for the LAN link (pick one).**

| Option | How | Trade-off |
|---|---|---|
| A. Second run to the media room | One of the 5 unidentified ports (P2-0) may go there. Patch it from the panel switch to the media room TP-Link switch | Simplest; nothing moves. Depends on a run existing |
| B. Share B2 with VLANs | Carry the AT&T WAN and the LAN as separate VLANs over B2 | Needs 802.1Q-capable switches at both ends; a config mistake can take the internet down |
| C. Move the gateway and hub to the panel | ONT, then BGW210, then hub, then panel switch; B2 becomes the LAN link to the media room switch | Cleanest long term; requires moving gear and power at the panel |
| D. Stopgap over MoCA | Add an ECB6200 at the panel (for example on the splitter's spare IN port) and link the panel switch through it | Rooms on Cat6 share the same MoCA capacity, so most of the benefit is lost |

**Decision (October 2026): option C.** The media room cabinet has a single Cat6e line (B2). Two other Cat6e runs end in POTS wall plates elsewhere in the media room, far from the cabinet, so option A does not help.

**Parts.** Gigabit switch (5 to 8 ports is enough to start), short Cat6 patch cables, power at the panel, a small shelf or mounting plate, and optionally a UPS (P2-7).

**Steps (option C).**
1. Confirm power, ventilation and space at the garage panel for the BGW210, hub, switch and a UPS.
2. Screenshot the BGW210 and Google Wifi settings.
3. Install and power the switch at the panel.
4. Move the BGW210 to the panel and patch B1 (the ONT feed) to its WAN port.
5. Move the hub to the panel: BGW210 LAN to hub WAN, hub LAN to the panel switch.
6. Patch B2 into the panel switch. At the media cabinet, plug the B2 jack into the TP-Link switch. That switch still feeds the media room devices and the ECB6200, so MoCA and the mesh nodes keep working unchanged.
7. Patch the finished RJ45 rooms (upstairs middle, basement office) into the panel switch.
8. Then do P2-4 (IP Passthrough).

**Consequence to plan for.** The hub also provides Wi-Fi, so moving it to the garage removes the hub's own Wi-Fi in the media room. Test first: a neighboring node (for example the sitting room node) may already cover it. If not, add a fifth Google Wifi unit or an access point in the media room, connected to the TP-Link switch.

**Considered and rejected: move only the gateway and put Google Wifi in bridge mode.** Google documents bridge mode for a single Wifi device only; in a multi-unit mesh the primary must stay a router (the other units already act as bridges). Without a router-mode primary, a switch at the panel would sit on the BGW210's side and its rooms would not share the Wi-Fi network. See [Google's bridge mode page](https://support.google.com/wifi/answer/6240987?hl=en).

**Check.** The hub gets its WAN address from the BGW210; each patched room negotiates 1 Gbps full duplex; iperf3 to the hub shows about 940 Mbps.

**Rollback.** Move the BGW210 and hub back to the media cabinet and re-patch B2 to the BGW210 WAN port.

## P2-3: Finish the coax cleanup

What is already good: the GE splitter is rated 5 to 2500 MHz, which covers the full MoCA band, and the unused RG6 runs are not connected to the splitter.

**Remaining.**
- Put a **75-ohm terminator** on the unused IN port (it currently has a plastic cap, which is not a terminator).
- Identify the gold right-angle adapter on one leg; replace it with a straight compression connector if it is not a rated F-to-F adapter.
- **Street RG6 drop (to the AT&T curb box):** its far end is unknown, so treat it as possibly live. Do not open the AT&T box; ask AT&T if you want it confirmed or removed. Leave it **disconnected from the splitter**. If it is ever connected (for example, a cable-provider service), first install a **MoCA POE filter** between the drop and the splitter so MoCA signals cannot leak out and outside signal cannot enter, and confirm the drop has a ground block (an electrician or the provider can check this).
- Optional: the master's second RG6 run is spare.

**Check.** MoCA status page on each ECB6200 shows stable link rates between all nodes.

## P2-4: Remove double NAT

**Problem.** The BGW210 is on default settings with Wi-Fi off, and the Google Wifi hub sits behind it as a second router (double NAT).

**Fix.** Turn on **IP Passthrough** on the BGW210 (admin page, usually 192.168.1.254, under Firewall) and assign the Google Wifi hub as the passthrough device. Keep BGW210 Wi-Fi off.

**Steps.**
1. Screenshot current BGW210 settings.
2. Enable IP Passthrough for the hub.
3. Reboot the hub; confirm it receives the public IP.

**Check.** Hub WAN address is public, not a 192.168.x, 10.x or 172.16 to 31.x address.

**Rollback.** Set passthrough back to off and reboot both devices.

## P2-5: Move mesh backhaul from MoCA to Cat6

**Today.** Backhaul is wired but runs over the shared MoCA 2.0 channel. A single test measured 890 Mbps upload over MoCA against 960 Mbps at the gateway, so the expected gain from Cat6 is small (up to roughly 70 Mbps, plus lower latency and no sharing between rooms). Treat this as low priority unless tests with several rooms active show MoCA slowing down.

**Fix.** For each node that has an identified Cat6e port **and an RJ45 room-end jack (P2-1)**, plug the Google Wifi node's WAN port into that jack, patched to the panel switch (P2-2). Leave MoCA for any room with no usable Cat6e run, and as a fallback. This applies to the top floor back right and sitting room nodes (and the master has no node).

**Basement play room node (higher value than the others).** It has no wired backhaul, so it is the one node where a cable would change performance noticeably. The basement office is already RJ45 at the room end: if the office is close, a patch from the panel switch to the office jack plus a short run or adapter could give it a wired uplink. Otherwise consider a MoCA adapter if a coax outlet is in the play room (RG6 runs there are unused and not connected at the panel today).

**Check.** Google Home app shows the node healthy; a wired speed test from the node's LAN port approaches line rate.

## P2-6: Label and document

- Number each run on both ends (for example C-A1) and keep [data/patch-panel.csv](data/patch-panel.csv) current.
- Photograph the panel before and after changes.
- Export device configs; do not commit passwords.
- Fill in the [inventory](03-inventory-template.md).

## P2-7: Power protection

Put the ONT, BGW210, hub and core switch on a UPS. Check ONT power arrangements with AT&T.

## P2-8: Optional

- **MoCA 2.5 adapters** if any room must stay on coax and needs more than MoCA 2.0 can deliver.
- **PoE access points** wired back to the panel switch to replace Google Wifi nodes (AC1200-class, 2016 generation). **Likely the strongest performance upgrade**, but confirm first: Wi-Fi upload was 256 Mbps at a spot midway between two nodes (worst case) while wired paths reach 890 to 960 Mbps. Retest beside a node; if it is much higher, node placement may matter more than new hardware.
- **Plan tier**: the gateway measured 960 Mbps, consistent with a 1 Gbps plan. The BGW210 and Google Wifi ports are gigabit, so a plan above 1 Gbps would need new gateway and switching hardware.

## Suggested order of work

1. P2-0 identify ports, P2-1 verify and test, P2-6 label
2. P2-4 IP Passthrough
3. P2-3 terminate IN port
4. P2-2 add switch, then P2-5 move nodes one room at a time
5. P2-7 UPS
6. P2-8 as needed
