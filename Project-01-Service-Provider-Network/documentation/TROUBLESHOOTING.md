# Troubleshooting history and open findings

## 1. Recovery veth links missing after Docker restart

**Observed:** FRR initialization logged repeated `eth1 not available after 60 seconds`; earlier Docker restart had disconnected five recovery-lab Containerlab veth links.

**Resolution used:** A topology-scoped `containerlab deploy` reconciled the recovery topology in place. Subsequent checks showed the required interfaces up. Avoid generic Docker restarts and destructive commands. Preserve original and persistence environments.

## 2. Incorrect VPN route targets after startup

**Observed:** Earlier effective RT values were PE1 `172.16.1.1:2` and PE2 `172.16.2.1:2`, rather than shared customer RT `65000:100`.

**Resolution used:** `topology/tests/full-recovery/recover-vpn-rt.sh` applied `rt vpn both 65000:100` on both PEs. VPN routes then imported and installed successfully. The exact mechanism of the original unexpected values was not conclusively established.

## 3. Configured export RD differs from effective VPNv4 RD

| PE | Saved / running config | Observed effective VPNv4 RD |
|---|---|---|
| PE1 | `rd vpn export 65000:11` | `172.16.1.1:2` |
| PE2 | `rd vpn export 65000:12` | `172.16.2.1:2` |

**Status:** Open, non-blocking. Both effective RDs are unique, RTs match `65000:100`, VPN prefixes are exchanged, and customer traffic passes. The underlying FRR behavior has not been conclusively diagnosed. No speculative configuration change was made to the working recovery lab.

## 4. Unqualified CE1-to-CE2 ping failed

**Observed:** `ping -c 5 -W 2 192.168.20.1` from CE1 returned 100% loss, despite routes and MPLS entries being present.

**Follow-up:** Explicit loopback-source tests succeeded in both directions:

```bash
docker exec clab-sp-full-recovery-test-CE1 ping -I 192.168.10.1 -c 5 -W 2 192.168.20.1
docker exec clab-sp-full-recovery-test-CE2 ping -I 192.168.20.1 -c 5 -W 2 192.168.10.1
```

**Interpretation:** Source-address selection is the leading explanation. The failed ping's actual source was not captured, so the exact cause is **not independently proven**. Use explicit customer loopback sources for the project's acceptance tests.

## 5. `show bgp ... route-leak` output was not applicable

FRR route-leak diagnostics displayed `routeDistinguisher: none` in a unicast VRF-to-VRF context. Those commands do not establish whether MPLS VPNv4 RD configuration is effective. The VPNv4 routing table is the appropriate place to inspect advertised RDs.
