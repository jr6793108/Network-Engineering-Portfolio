# Recovery and verification runbook

## Safety and scope

Operate only on the isolated recovery lab `sp-full-recovery-test`. Before any recovery activity, confirm the original network has six running containers, the persistence network has two, and the recovery network has six. Validate each target container's `containerlab` Docker label before accessing it. Do not restart containers merely to reload FRR: a prior Docker restart severed Containerlab veth connections.

## Working files

From `~/Network-Engineering-Portfolio/Project-01-Service-Provider-Network`:

```text
topology/tests/full-recovery/full-recovery.clab.yml
topology/tests/full-recovery/configs/PE1/frr.conf
topology/tests/full-recovery/configs/PE2/frr.conf
topology/tests/full-recovery/recover-vpn-rt.sh
scripts/init-mpls-network-v2.sh
scripts/start-frr-with-mpls-v2.sh
```

## Startup design

The initialization script waits for `eth1` and `eth2`, creates and activates VRF `CUSTOMER-A` (table 1001) on provider edges, enslaves `eth2` to the VRF, and enables relevant MPLS kernel settings. FRRouting starts afterward via `/usr/lib/frr/docker-start`. Saved PE configurations define the VPNv4 peer, export RD, RT import/export, auto VPN label, and `export vpn` / `import vpn`.

## Historical recovery actions

1. A prior `docker restart` caused missing recovery-lab veth links and interface wait errors. The recovery topology was reconciled in place using a topology-scoped Containerlab deploy, restoring links. **Do not repeat without a scoped dry run and confirming protected environments.**
2. The recovery RT correction script `recover-vpn-rt.sh` was used to apply `rt vpn both 65000:100` to both PEs. It was reported as scope-verified and idempotent.
3. MP-BGP VPNv4 exchange and CUSTOMER-A routing tables were verified.
4. Customer loopback pings were tested in both directions with explicit source addresses.
5. All original, persistence, and recovery container counts were rechecked.

## Read-only verification commands

Run from the project root. These commands do not change configurations:

```bash
# Environment inventory
docker ps --filter 'label=containerlab=sp-routing-lab'
docker ps --filter 'label=containerlab=sp-persistence-test'
docker ps --filter 'label=containerlab=sp-full-recovery-test'

# Verify a specific recovery container before inspecting it
docker inspect clab-sp-full-recovery-test-PE1 \
  --format '{{index .Config.Labels "containerlab"}}'

# MP-BGP VPNv4
docker exec clab-sp-full-recovery-test-PE1 vtysh -c 'show bgp ipv4 vpn summary'
docker exec clab-sp-full-recovery-test-PE2 vtysh -c 'show bgp ipv4 vpn summary'

# VRF and MPLS
docker exec clab-sp-full-recovery-test-PE1 ip link show master CUSTOMER-A
docker exec clab-sp-full-recovery-test-PE1 ip route show vrf CUSTOMER-A
docker exec clab-sp-full-recovery-test-PE2 ip route show vrf CUSTOMER-A
docker exec clab-sp-full-recovery-test-PE1 ip -f mpls route show
docker exec clab-sp-full-recovery-test-PE2 ip -f mpls route show

# Explicit customer loopback-to-loopback testing
docker exec clab-sp-full-recovery-test-CE1 ping -I 192.168.10.1 -c 5 -W 2 192.168.20.1
docker exec clab-sp-full-recovery-test-CE2 ping -I 192.168.20.1 -c 5 -W 2 192.168.10.1
```

## Acceptance criteria

- Six original, two persistence, and six recovery containers remain running.
- MP-BGP VPNv4 sessions between `10.255.0.11` and `10.255.0.12` are established and VPN routes exchanged.
- CUSTOMER-A is up with `eth2` attached on both PEs and remote MPLS routes installed.
- Both explicit-source CE loopback pings pass.

**Scope note:** This document records the successful recovery validation, not a guarantee of restart persistence under every future host or software condition.
