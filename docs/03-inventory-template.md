# Inventory

Pre-filled with the confirmed as-found values. Copy the tables into your own notes to document another house. Do not record passwords here.

## Devices

| Tag | Role | Make / model | Firmware | Location | Notes |
|---|---|---|---|---|---|
| ONT | Fiber terminal | AT&T external device | n/a | Exterior wall | AT&T Cat6 to panel port B1 |
| GW | Gateway | AT&T BGW210 | TBD | Media room | Default settings, Wi-Fi off |
| HUB | Mesh primary | Google Wifi AC-1304 | 14150.376.32 | Media room | Router mode behind GW (double NAT) |
| SW1 | Switch | TP-Link, model TBD | TBD | Media room | Feeds wired devices and MC0 |
| MC0 | MoCA adapter | Actiontec ECB6200 | TBD | Media room | MoCA 2.0, gigabit port |
| MC1 | MoCA adapter | Actiontec ECB6200 (assumed) | TBD | Second floor back right | |
| MC2 | MoCA adapter | Actiontec ECB6200 (assumed) | TBD | Sitting room media cabinet | |
| MC3 | MoCA adapter | Actiontec ECB6200 (assumed) | TBD | Master (1 of 2 coax runs) | |
| N1..N3 | Mesh nodes | Google Wifi AC-1304 | 14150.376.32 | Rooms of MC1..MC3 | Backhaul over MoCA, WAN port to MoCA |
| SPL | Coax splitter | GE digital 4-way, 5-2500 MHz | n/a | Garage comm panel | IN unused; 4 outputs used |

## Cable runs

Patch panel (garage), 12 keystone ports. Full table in [data/patch-panel.csv](data/patch-panel.csv).

| Run ID | Type | Panel port | Room end today | Tested date | Result |
|---|---|---|---|---|---|
| C-A1 | Cat6e | A1 Great Room | POTS unless one of the 2 RJ45 rooms | | |
| C-A2 | Cat6e | A2 Master | same | | |
| C-A4 | Cat6e | A4 Upstairs Center | RJ45 (upstairs middle, assumed same) | | |
| C-B1/B2 | Cat6e | B1 ATT IN, B2 ATT Router | RJ45 (carries Ethernet) | | |
| C-B5 | Cat6e | B5 Kitchen | same | | |
| C-B6 | Cat6e | B6 Attic Cable | same | | |
| C-A3, A5, A6, B3, B4 | Cat6e | UNK | unknown | | |
| R-01..04 | RG6 | splitter outputs | media room, sitting room media cabinet, second floor back right, master | | |
| R-05..n | RG6 | not connected | not connected at either end | | |

## Addressing

| Item | Value |
|---|---|
| WAN type | AT&T Fiber |
| LAN subnet | TBD |
| DHCP server | BGW210 and Google Wifi hub (double NAT) |
| IP Passthrough | Off (default) |
| Double NAT? | Yes |

## Measured baselines (before any change)

| Path | Down (Mbps) | Up (Mbps) | Latency (ms) | Date |
|---|---|---|---|---|
| ISP speed test at gateway | | | | |
| Media room wired to internet | | | | |
| Each MoCA room wired to internet | | | | |
| Each room Wi-Fi to internet | | | | |
