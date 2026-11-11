# Service Provider MPLS L3VPN — Six-Router Recovery Validation

**Status:** Recovery validation complete (11 November 2026).  
**Platform:** Ubuntu, Containerlab, FRRouting 10.3.1_git, Docker.  
**Topology:** CE1 — PE1 — P1 — P2 — PE2 — CE2.

## Project scope

This project validates a six-router service-provider lab with an IS-IS Level 2 underlay, MPLS/LDP transport, MP-BGP VPNv4 exchange, and a CUSTOMER-A Layer 3 VPN. The isolated recovery environment was validated without modifying the original or persistence environments.

| Environment | Containerlab name | Verified running |
|---|---|---|
| Original | `sp-routing-lab` | 6/6 |
| Persistence | `sp-persistence-test` | 2/2 |
| Isolated recovery | `sp-full-recovery-test` | 6/6 |

> **Safety:** Do not destroy, restart, or modify the original or persistence environments. Avoid unscoped `containerlab destroy`, `docker restart`, or `docker system prune`. All documented recovery operations are scoped to `sp-full-recovery-test`.

## Architecture

```text
CE1                 Service-provider backbone                  CE2
192.168.10.1/32                                             192.168.20.1/32
  |                                                               |
172.16.1.2                                                     172.16.2.2
  |                                                               |
PE1 (10.255.0.11) -- P1 -- P2 -- PE2 (10.255.0.12)
  |                                                               |
CUSTOMER-A VRF                                               CUSTOMER-A VRF
Table 1001                                                    Table 1001

Underlay: IS-IS Level 2 | Transport: MPLS/LDP | Overlay: MP-BGP VPNv4
BGP AS: 65000 | VPN route target: 65000:100 | VPN label: 80
```

The recovery management network is `sp-full-recovery-mgmt` (`10.250.252.0/24`). It is separate from the original network (`10.250.250.0/24`).

## Validated outcomes

- IS-IS routing and MPLS/LDP operation were verified during recovery testing.
- PE1 and PE2 VPNv4 BGP sessions were established, each sending and receiving one VPN prefix.
- `eth2` on each PE is attached to `CUSTOMER-A` (VRF table 1001).
- PE1's remote customer route uses MPLS stack `19/80`; PE2's uses `18/80`.
- **CE1 → CE2:** five of five ICMP replies, 0% loss, average 0.066 ms, with source `192.168.10.1`.
- **CE2 → CE1:** five of five ICMP replies, 0% loss, average 0.066 ms, with source `192.168.20.1`.
- All three Containerlab environments were running at the end of the final verification.

## Documentation

- [Architecture and addressing](documentation/ARCHITECTURE.md)
- [Recovery and verification procedure](documentation/RECOVERY-RUNBOOK.md)
- [Troubleshooting and open findings](documentation/TROUBLESHOOTING.md)
- [Completion report](documentation/COMPLETION-REPORT.md)
- [Evidence inventory](evidence/README.md)

## Repository paths in the working lab

- Topology: `topology/tests/full-recovery/full-recovery.clab.yml`
- Recovery FRR configurations: `topology/tests/full-recovery/configs/{PE1,PE2}/frr.conf`
- RT correction script: `topology/tests/full-recovery/recover-vpn-rt.sh`
- Initialization: `scripts/init-mpls-network-v2.sh`
- FRR startup: `scripts/start-frr-with-mpls-v2.sh`
- Recorded evidence: `topology/tests/full-recovery/evidence/post-recovery-20261111-002916/`

These are references to files in the Ubuntu project workspace; they are **not included** in this documentation-only package. Copy them separately if publishing the complete lab implementation. Review configurations and logs for credentials or other sensitive information before publication.
