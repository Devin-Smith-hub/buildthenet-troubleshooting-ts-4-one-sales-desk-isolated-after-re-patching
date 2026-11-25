# Troubleshooting #4: One Sales Desk Isolated After Re-Patching

_Level: Easy — BuildTheNet 3D Sandbox troubleshooting — VLANs_

**Status: Verified.** Completed on 2026-11-25; every requirement was machine-checked against the network built in BuildTheNet and passed.

Snapshot taken 2026-11-25. Built in a simulation — no physical or production equipment was involved.

## The ticket

**Reported symptom:** PC-2 can't reach other Sales PCs or the Sales printer; the rest of Sales is fine.

Helpdesk Ticket #104: Facilities re-patched the Sales pod yesterday. Today PC-2 cannot reach PC-1, PC-3 or the Sales printer, all of which are on the same switch and the same 10.10.10.0/24 range. The reception kiosk on the same switch sits in the GUEST VLAN with an old Sales-range address, and security relies on the VLAN to keep it away from Sales.

_Area: VLANs_

## Required resolution

Get PC-2 back into the Sales network so it reaches PC-1 and the printer, keeping the guest kiosk separate and every other Sales device working.

## Checks

| # | Check | Status | Checked by |
|---|---|---|---|
| 1 | PC-2 to PC-1 works | Passed | Sandbox check |
| 2 | PC-2 to Sales-Printer works | Passed | Sandbox check |
| 3 | PC-1 to Sales-Printer still works | Passed | Sandbox check |
| 4 | PC-3 to Sales-Printer still works | Passed | Sandbox check |
| 5 | Guest-Kiosk to Sales-Printer stays blocked | Passed | Sandbox check |
| 6 | PC-1 keeps its documented addressing | Passed | Sandbox check |
| 7 | PC-2 keeps its documented addressing | Passed | Sandbox check |
| 8 | PC-3 keeps its documented addressing | Passed | Sandbox check |
| 9 | Sales-Printer keeps its documented addressing | Passed | Sandbox check |
| 10 | Guest-Kiosk keeps its documented addressing | Passed | Sandbox check |

## Root cause

SW-Access Fa0/2 (PC-2) is an access port in VLAN 20 (GUEST); the Sales devices are in VLAN 10.

## The fix

What was changed to resolve the ticket, compared with the network as it was reported:

Compared with the starting topology, the learner changed the configuration of 1 device.

**SW-Access**

```diff
  interface FastEthernet0/2
-  switchport access vlan 20
+  switchport access vlan 10
```

## Result

Every check passed on this snapshot:

> PC-2 is back in the Sales VLAN and reaches the pod; the guest kiosk is still kept out.

## Topology

![Topology diagram](topology.svg)

## Addressing

| Device | Interface | Address | Mask | Mode | Gateway |
|---|---|---|---|---|---|
| PC-1 | FastEthernet0 | 10.10.10.11/24 | 255.255.255.0 | VLAN 1 |  |
| PC-2 | FastEthernet0 | 10.10.10.12/24 | 255.255.255.0 | VLAN 1 |  |
| PC-3 | FastEthernet0 | 10.10.10.13/24 | 255.255.255.0 | VLAN 1 |  |
| Sales-Printer | FastEthernet0 | 10.10.10.50/24 | 255.255.255.0 | VLAN 1 |  |
| Guest-Kiosk | FastEthernet0 | 10.10.10.90/24 | 255.255.255.0 | VLAN 1 |  |

## Device configurations

Running-configs as `show running-config` prints them in the BuildTheNet sandbox. Passwords, secrets, keys and SNMP communities are replaced with `<removed>`.

- [SW-Access](configs/SW-Access.txt)

---

Built on [BuildTheNet](https://www.buildthenet.io/sandbox)
