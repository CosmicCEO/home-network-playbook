# Phase 2: Improvement Opportunities

Prioritized from highest value per effort, based on the confirmed as-found configuration. Nothing here has been applied except where marked **Done**.

## Summary

| ID | Opportunity | Status | Effort | Cost (rough) | Impact |
|---|---|---|---|---|---|
| P2-0 | Identify and label the 5 unknown patch-panel ports | To do | Low | Free to low | High (prerequisite) |
| P2-1 | Re-terminate Cat6e from POTS plugs to RJ45 keystones | **Panel end done; 2 rooms done at the room end**; rest to do | Medium | Low | Very high |
| P2-2 | Add a switch at the garage panel and patch rooms to it | To do | Medium | Medium | **Very high** |
| P2-3 | Finish the coax cleanup (cap IN port, check adapter) | To do | Low | Low | Medium |
| P2-4 | Remove double NAT with BGW210 IP Passthrough | To do | Low | Free | High |
| P2-5 | Move mesh backhaul from MoCA to Cat6 where a run exists | To do | Low | Free | High |
| P2-6 | Label, document, back up configs | Partly done (panel sheet) | Low | Low | Medium |
| P2-7 | UPS for ONT, gateway, switch | To do | Low | Medium | Medium |
| P2-8 | Optional: MoCA 2.5, PoE access points, newer mesh | Optional | Medium | Medium | Situational |

## P2-0: Identify the unknown ports

**Problem.** Five of the 12 patch-panel ports (A3, A5, A6, B3, B4) are labeled UNK, and the labeled ones (Great Room, Master, Upstairs Center, Kitchen, Attic Cable) are unconfirmed as to where they land. Every later step depends on knowing which port serves which room.

**Fix.** Use a tone generator and probe, or a cable tester with remote ends, to trace each port to its room jack. Update [data/patch-panel.csv](data/patch-panel.csv) and the table in the as-found doc.

**Check.** Every port has a room and wall-plate location; every room that has coax also has a known Cat6e port (or is marked as having none).

## P2-1: Cat6e termination (panel done, rooms partly done)

**Status.** The panel end is complete: every run is on an RJ45 keystone on the patch panel. At the **room end**, only **two rooms** have been converted to RJ45; the remaining rooms are still POTS-style plugs, which use only 2 to 4 of the 8 conductors and cannot carry gigabit Ethernet.

**Remaining.**
1. Record which two rooms are done (open question O-2).
2. Re-terminate each remaining room-end jack from POTS to a Cat6 RJ45 keystone (same wiring standard as the panel, 568B or 568A).
3. Test every run with a wire-map cable tester (all 8 conductors straight through).
4. Record lengths; permanent links must be under 90 m (295 ft).
5. Prioritize rooms that currently depend on MoCA (second floor back right, sitting room, master), once P2-0 shows which ports serve them.

**Check.** Wire map passes; an iperf3 test across a patched run reaches about 940 Mbps on gigabit.

## P2-2: Add a switch at the panel

**Problem.** The patch panel exists, but there is no switch at the garage panel, so the Cat6e runs cannot carry room traffic. Remote rooms still depend on the shared MoCA 2.0 channel.

**Fix.** Install a gigabit switch (PoE if access points are planned) at the panel. Patch each room's port to it. Connect the switch to the Google Wifi hub's LAN port (or to the BGW210 once passthrough is in place; see P2-4).

**Parts.** Gigabit switch, short Cat6 patch cables, a power outlet at the panel (or a UPS), mounting shelf.

**Steps.**
1. Confirm power and ventilation at the garage panel.
2. Connect the switch to the hub.
3. Patch room runs one at a time; confirm link and 1 Gbps negotiation.
4. Keep MoCA in place until each room is proven on Cat6.

**Check.** Each patched room negotiates 1 Gbps full duplex; iperf3 to the hub shows about 940 Mbps.

**Rollback.** Unplug the patch cable; the MoCA path is untouched.

## P2-3: Finish the coax cleanup

What is already good: the GE splitter is rated 5 to 2500 MHz, which covers the full MoCA band, and the unused RG6 runs are not connected to the splitter.

**Remaining.**
- Put a **75-ohm terminator** on the unused IN port (it currently has a plastic cap, which is not a terminator).
- Identify the gold right-angle adapter on one leg; replace it with a straight compression connector if it is not a rated F-to-F adapter.
- **Street RG6 drop (to the AT&T curb box):** its far end is unknown, so treat it as possibly live. Do not open the AT&T box; ask AT&T if you want it confirmed or removed. Leave it **disconnected from the splitter**. If it is ever connected (for example, a cable-provider service), first install a **MoCA POE filter** between the drop and the splitter so MoCA signals cannot leak out and outside signal cannot enter, and confirm the drop has a ground block (an electrician or the provider can check this).
- Optional: note the master's second RG6 run is spare, and could be used if a second node is ever needed.

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

**Today.** Backhaul is wired but runs over the shared MoCA 2.0 channel.

**Fix.** For each remote room that has an identified Cat6e port **and an RJ45 room-end jack (P2-1)**, plug the Google Wifi node's WAN port into that jack, patched to the panel switch (P2-2). Leave MoCA for any room with no usable Cat6e run, and as a fallback.

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
- **PoE access points** wired back to the panel switch to replace Google Wifi nodes (AC1200-class, 2016 generation).
- **Plan tier check**: the BGW210 and Google Wifi ports are gigabit, so a plan above 1 Gbps would need new gateway and switching hardware.

## Suggested order of work

1. P2-0 identify ports, P2-1 verify and test, P2-6 label
2. P2-4 IP Passthrough
3. P2-3 terminate IN port
4. P2-2 add switch, then P2-5 move nodes one room at a time
5. P2-7 UPS
6. P2-8 as needed
