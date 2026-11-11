# Architecture and addressing

## Logical topology

`CE1 ↔ PE1 ↔ P1 ↔ P2 ↔ PE2 ↔ CE2`

| Node | Role | Known addressing |
|---|---|---|
| CE1 | Customer endpoint | Loopback `192.168.10.1/32`; `eth1` `172.16.1.2/30` |
| PE1 | Provider edge | Loopback `10.255.0.11/32`; `eth1` `10.0.11.2/30`; `eth2` `172.16.1.1/30` |
| P1 | Provider core | See topology/configuration files for interface addressing |
| P2 | Provider core | See topology/configuration files for interface addressing |
| PE2 | Provider edge | Loopback `10.255.0.12/32`; `eth1` `10.0.22.2/30`; `eth2` `172.16.2.1/30` |
| CE2 | Customer endpoint | Loopback `192.168.20.1/32`; `eth1` `172.16.2.2/30` |

## Protocol design

- IS-IS CORE, Level 2, provides provider-underlay routing.
- MPLS LDP provides transport label distribution across the backbone.
- MP-BGP VPNv4 runs between PE loopbacks in AS 65000.
- Linux VRF `CUSTOMER-A` uses routing table 1001; customer-facing `eth2` is its slave interface on both PEs.
- Route target `65000:100` is used for both import and export.
- Local VPN label `80` is installed on both PEs.

## Installed customer routes

**PE1 CUSTOMER-A**

```text
172.16.1.0/30 dev eth2 src 172.16.1.1
192.168.10.1 via 172.16.1.2 dev eth2
192.168.20.1 encap mpls 19/80 via 10.0.11.1 dev eth1
```

**PE2 CUSTOMER-A**

```text
172.16.2.0/30 dev eth2 src 172.16.2.1
192.168.10.1 encap mpls 18/80 via 10.0.22.1 dev eth1
192.168.20.1 via 172.16.2.2 dev eth2
```

The MPLS label stacks above were observed in installed Linux routing tables. Packet-level label capture was not part of the final check.
