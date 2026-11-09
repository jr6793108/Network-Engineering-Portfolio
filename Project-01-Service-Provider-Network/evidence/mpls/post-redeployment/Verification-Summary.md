# Project 01 — MPLS Post-Redeployment Verification

## Network
PE1 -- P1 -- P2 -- PE2

## Routing and Label Distribution
- IS-IS Level 2 adjacencies verified
- LDP neighbor sessions operational
- MPLS forwarding tables populated
- Linux kernel MPLS forwarding entries installed

## Data-Plane Validation
- Source: PE1 (10.255.0.11)
- Destination: PE2 (10.255.0.12)
- ICMP: 15 transmitted, 15 received
- Packet loss: 0%
- Capture interface: P1 eth2
- MPLS Ethertype: 0x8847
- Observed MPLS label: 19
- MPLS packets captured: 15

## Evidence
- P1-MPLS.pcap

## Conclusion
PASS: MPLS-encapsulated ICMP traffic was
successfully captured after redeployment.

Control-plane and data-plane verification completed.
