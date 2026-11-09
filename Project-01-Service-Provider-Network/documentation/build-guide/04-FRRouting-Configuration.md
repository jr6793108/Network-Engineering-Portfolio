# Project 01 - FRRouting Configuration

## Purpose

Configure four FRRouting routers to provide
IS-IS Level 2 routing and MPLS LDP forwarding.

## Router Roles

- PE1: Provider Edge
- P1: Provider Core
- P2: Provider Core
- PE2: Provider Edge

## Configuration Files

Each router uses:

- daemons: Enables FRRouting services
- frr.conf: Router configuration
- vtysh.conf: CLI configuration

## IS-IS Configuration

IS-IS provides reachability between router
loopback addresses and transport interfaces.

The routing process is named CORE.

Example configuration:

router isis CORE
 is-type level-2-only
 net 49.0001.0000.0000.0011.00

Interface configuration:

interface eth1
 ip router isis CORE
 isis circuit-type level-2-only
 isis network point-to-point

The NET identifies the IS-IS router.
Each router must have a unique system ID.

## MPLS LDP Configuration

LDP distributes MPLS labels between routers.

Example:

mpls ldp
 router-id 10.255.0.11
 address-family ipv4
  discovery transport-address 10.255.0.11
  interface eth1
  exit
 exit

Core routers P1 and P2 also enable
LDP on their second transport interface.

## Linux MPLS Settings

Enable IPv4 forwarding:

sysctl -w net.ipv4.ip_forward=1

Configure MPLS label capacity:

sysctl -w net.mpls.platform_labels=100000

Enable MPLS processing:

sysctl -w net.mpls.conf.eth1.input=1

On routers with eth2:

sysctl -w net.mpls.conf.eth2.input=1

These settings must be applied in the
appropriate router network namespace.

## Verification Commands

Check IS-IS neighbors:

vtysh -c "show isis neighbor"

Check IS-IS routes:

vtysh -c "show ip route isis"

Check LDP neighbors:

vtysh -c "show mpls ldp neighbor"

Check MPLS label table:

vtysh -c "show mpls table"

Check Linux MPLS forwarding:

ip -f mpls route show

## Expected Results

- IS-IS adjacencies established
- LDP sessions operational
- MPLS forwarding labels installed
- PE1 and PE2 loopbacks reachable
- MPLS encapsulated packets observed
