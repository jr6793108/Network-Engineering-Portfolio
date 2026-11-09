# Project 01 - MPLS Verification

## Objective

Verify the service provider MPLS transport
network after deployment and redeployment.

## Network Topology

PE1 --- P1 --- P2 --- PE2

## Step 1 - Verify Container Status

Command:

sudo containerlab inspect \
  -t topology/service-provider.clab.yml

Expected result:

All four FRRouting containers running.

## Step 2 - Verify IS-IS Neighbors

Command:

docker exec clab-sp-routing-lab-P1 \
  vtysh -c "show isis neighbor"

Expected result:

P1 has IS-IS Level 2 adjacencies
with PE1 and P2.

## Step 3 - Verify LDP Neighbors

Command:

docker exec clab-sp-routing-lab-P1 \
  vtysh -c "show mpls ldp neighbor"

Expected result:

LDP neighbors operational.

## Step 4 - Verify MPLS Label Tables

Command:

docker exec clab-sp-routing-lab-P1 \
  vtysh -c "show mpls table"

Expected result:

MPLS labels installed.

## Step 5 - Verify Linux MPLS Forwarding

Command:

docker exec clab-sp-routing-lab-P1 \
  ip -f mpls route show

Expected result:

Linux kernel MPLS forwarding
entries installed.

## Step 6 - Verify End-to-End Connectivity

PE1 to PE2:

docker exec clab-sp-routing-lab-PE1 \
  ping -c 15 -I 10.255.0.11 10.255.0.12

PE2 to PE1:

docker exec clab-sp-routing-lab-PE2 \
  ping -c 15 -I 10.255.0.12 10.255.0.11

Verified results:

15 packets transmitted
15 packets received
0% packet loss

## Step 7 - Verify MPLS Encapsulation

Packet capture interface:

P1 eth2

Capture filter:

ether proto 0x8847

Verified packet:

MPLS label: 19
EtherType: 0x8847
Traffic: ICMP echo request

Source: 10.255.0.11
Destination: 10.255.0.12

Packet capture:

evidence/mpls/post-redeployment/P1-MPLS.pcap

## Conclusion

IS-IS routing verified.

LDP label distribution verified.

Linux MPLS forwarding verified.

End-to-end connectivity verified.

MPLS encapsulation verified.

Project 01 MPLS transport validation PASS.
