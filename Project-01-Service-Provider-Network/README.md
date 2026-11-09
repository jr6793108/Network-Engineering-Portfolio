# Project 01 - Service Provider Network

## Overview

This project implements a service provider routing
environment using Containerlab, Docker, and FRRouting.

The lab demonstrates IS-IS routing, MPLS forwarding,
LDP label distribution, and packet-level verification.

## Network Architecture

PE1 --- P1 --- P2 --- PE2

The four-router MPLS transport network consists of:

| Router | Role | Loopback |
|--------|------|----------|
| PE1 | Provider Edge | 10.255.0.11 |
| P1 | Provider Core | 10.255.0.1 |
| P2 | Provider Core | 10.255.0.2 |
| PE2 | Provider Edge | 10.255.0.12 |

CE1 and CE2 directories are reserved for customer-edge
network expansion.

## Technologies

- Containerlab
- Docker
- FRRouting 10.3.1
- IS-IS Level 2
- MPLS
- LDP
- Linux MPLS forwarding
- tcpdump

## Implementation

1. Create the Containerlab topology.
2. Configure router interfaces and loopbacks.
3. Configure IS-IS Level 2 routing.
4. Verify routing adjacencies and route installation.
5. Enable Linux MPLS forwarding.
6. Configure MPLS LDP.
7. Verify LDP neighbor relationships.
8. Inspect MPLS forwarding and label tables.
9. Test end-to-end PE connectivity.
10. Capture MPLS-encapsulated traffic.
11. Redeploy the lab and verify persistence.

## Verification Results

### Control Plane

- IS-IS adjacencies operational
- LDP sessions established
- MPLS labels distributed
- Routing information exchanged

### Data Plane

- PE1 to PE2 connectivity verified
- PE2 to PE1 connectivity verified
- MPLS EtherType 0x8847 observed
- MPLS label 19 observed in packet captures
- 15 MPLS packets captured during validation
- 0% packet loss during successful verification

## Evidence

Verification artifacts are stored under:

- evidence/isis/
- evidence/mpls/final-verification/
- evidence/mpls/packet-capture/
- evidence/mpls/post-redeployment/

Packet captures can be inspected using Wireshark
or tcpdump.

## Rebuild Documentation

Detailed implementation and troubleshooting procedures
will be maintained in the documentation directory.

## Project Status

MPLS core implementation and post-redeployment
forwarding verification completed successfully.

Future expansion may include customer-edge routing,
BGP, MPLS L3VPN services, and failover testing.
