# Recovery validation completion report

**Validation completed:** 11 November 2026  
**Scope:** Isolated six-router Containerlab recovery environment `sp-full-recovery-test`  
**Outcome:** **PASS**, with documented non-blocking observations.

## Test matrix

| Test | Evidence / observed outcome | Status |
|---|---|---|
| Protected environment inventory | Original 6/6; persistence 2/2; recovery 6/6 | PASS |
| Recovery identity | CE1, PE1, P1, P2, PE2, CE2 labels verified | PASS |
| IS-IS and LDP | Previously verified operational in recovery tests | PASS |
| MP-BGP VPNv4 | Each PE had one established neighbor, one prefix sent and received | PASS |
| VRF attachment | PE1 and PE2 `eth2` master CUSTOMER-A, table 1001 | PASS |
| MPLS route installation | PE1 remote `19/80`, PE2 remote `18/80`; label 80 local | PASS |
| CE1 → CE2 | 5 sent / 5 received; 0% loss; average 0.066 ms | PASS |
| CE2 → CE1 | 5 sent / 5 received; 0% loss; average 0.066 ms | PASS |
| Protected environment recheck | Original 6/6; persistence 2/2; recovery 6/6 | PASS |

## Final BGP snapshot

- PE1 router ID `10.255.0.11`, neighbor `10.255.0.12`, AS 65000, one prefix received and sent.
- PE2 router ID `10.255.0.12`, neighbor `10.255.0.11`, AS 65000, one prefix received and sent.
- Both sessions had been up approximately 27 minutes at the recorded final health check.

## Findings retained

1. An unqualified ping failed, but loopback-sourced pings passed in both directions. Source selection is suspected, not proven.
2. Effective VPN RDs differed from configured RDs. Both remained unique and did not prevent service.

## Sign-off

Recovery functionality has been validated without further configuration changes. The recorded evidence supports the successful state at test time; it does not prove future reboot persistence. Further RD research is optional and should be isolated from the protected environments.
