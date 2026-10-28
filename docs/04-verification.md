# Verification Procedures

Run these before and after any change so improvements are measured, not assumed.

## 1. Physical (cables)

- **Cat6e**: use a cable tester with wire-map. All 8 conductors should map 1-1 (no splits, no opens). POTS-terminated runs will show only the pairs they were terminated with. Record length if the tester reports it.
- **RG6**: check continuity center-to-center and shield-to-shield with a coax tester; look for shorts. Visually inspect F-connectors.

## 2. Throughput (iperf3)

Server on a wired machine at the core, client in the room under test.

```bash
# server (core)
iperf3 -s

# client (room under test), 30 s, both directions
iperf3 -c <server-ip> -t 30
iperf3 -c <server-ip> -t 30 -R
```

Expected: about 940 Mbps over gigabit Ethernet; MoCA 2.0 typically well below that and drops with extra splitters.

## 3. Double NAT test

```bash
# Compare the router's WAN IP with the public IP
curl -s https://api.ipify.org ; echo
```

If the Nest hub's WAN address is a private range (10.x, 172.16 to 31.x, 192.168.x), you have double NAT. A traceroute showing two private hops before leaving your network confirms it:

```bash
traceroute -n 1.1.1.1    # macOS/Linux
tracert -d 1.1.1.1       # Windows
```

## 4. MoCA health

On each Actiontec adapter's status page (or its LED pattern) record: connected nodes, channel, link/PHY rate to each peer, signal level. Low PHY rate to one room points to splitter, connector or run problems on that leg.

## 5. Mesh backhaul

In the Google Home app, confirm each point reports a wired connection. A point showing wireless backhaul next to a live Ethernet jack means the cable is not being used.

## 6. Record

Write results into the "Measured baselines" table of the [inventory](03-inventory-template.md) with the date.
