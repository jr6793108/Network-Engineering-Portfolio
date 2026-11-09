# Project 01 - MPLS Troubleshooting and Recovery

## Objective

Document the troubleshooting procedures used to
restore MPLS forwarding and verify the network
after Containerlab redeployment.

## Network Topology

PE1 --- P1 --- P2 --- PE2

## Issue 1 - End-to-End Ping Failure

### Symptoms

PE1 could not ping PE2 loopback 10.255.0.12.

PE2 could not ping PE1 loopback 10.255.0.11.

Initial tests showed 100% packet loss.

### Investigation

Verify IS-IS neighbors:

docker exec clab-sp-routing-lab-P1 \
  vtysh -c "show isis neighbor"

Verify LDP neighbors:

docker exec clab-sp-routing-lab-P1 \
  vtysh -c "show mpls ldp neighbor"

Verify MPLS label tables:

docker exec clab-sp-routing-lab-P1 \
  vtysh -c "show mpls table"

Verify kernel MPLS routes:

docker exec clab-sp-routing-lab-P1 \
  ip -f mpls route show

### Root Cause

Linux MPLS input processing was disabled
on one or more transport interfaces.

The routing and label distribution control
planes were operational, but MPLS packets
could not be forwarded correctly.

### Resolution

Enable MPLS input processing on the
appropriate transport interfaces.

Example:

docker exec clab-sp-routing-lab-P1 \
  sysctl -w net.mpls.conf.eth1.input=1

docker exec clab-sp-routing-lab-P1 \
  sysctl -w net.mpls.conf.eth2.input=1

Repeat on other routers as appropriate.

### Verification

docker exec clab-sp-routing-lab-PE1 \
  ping -c 4 -I 10.255.0.11 10.255.0.12

Expected result:

0% packet loss.

## Issue 2 - MPLS Settings Lost After Redeployment

### Symptoms

MPLS forwarding settings required
reconfiguration after container recreation.

### Root Cause

Runtime sysctl changes are not automatically
persistent across container recreation.

### Resolution

Add MPLS configuration commands to
the Containerlab topology definition.

Example:

exec:
  - sysctl -w net.mpls.platform_labels=100000
  - sysctl -w net.mpls.conf.eth1.input=1

For routers with eth2:

  - sysctl -w net.mpls.conf.eth2.input=1

Ensure the required NET_ADMIN and SYS_ADMIN
capabilities are configured.

### Verification

Redeploy:

sudo containerlab deploy \
  -t topology/service-provider.clab.yml \
  --dry-run

Review the plan before applying changes.

Then deploy:

sudo containerlab deploy \
  -t topology/service-provider.clab.yml

Check MPLS settings:

docker exec clab-sp-routing-lab-P1 \
  sysctl net.mpls.conf.eth1.input

Expected value:

net.mpls.conf.eth1.input = 1

## Issue 3 - tcpdump Missing in FRRouting Container

### Symptoms

Attempting to execute tcpdump inside
the FRRouting container returned:

tcpdump: No such file or directory

### Root Cause

The FRRouting container image does not
include tcpdump.

### Resolution

Install tcpdump on the Ubuntu host:

sudo apt update

sudo apt install -y tcpdump

Use nsenter to capture traffic within
the container network namespace.

Example:

P1_PID=$(docker inspect -f '{{.State.Pid}}' \
  clab-sp-routing-lab-P1)

sudo timeout 20 nsenter -t "$P1_PID" -n \
  tcpdump -ni eth2 -e -vv \
  'ether proto 0x8847'

## Issue 4 - Packet Capture Shows Zero Packets

### Symptoms

tcpdump started successfully but captured
zero MPLS packets.

### Investigation

Confirm the correct interface.

docker exec clab-sp-routing-lab-P1 \
  ip -br addr show eth2

Ensure the packet capture runs while
traffic is generated.

### Resolution

Start tcpdump before generating traffic.

Generate ICMP traffic from PE1 to PE2.

docker exec clab-sp-routing-lab-PE1 \
  ping -c 15 -i 0.5 \
  -I 10.255.0.11 10.255.0.12

### Verification

Expected packet characteristics:

EtherType: 0x8847

MPLS label: 19

Source: 10.255.0.11

Destination: 10.255.0.12

## Final Validation

Verify:

1. All four containers running.
2. IS-IS adjacencies established.
3. LDP sessions operational.
4. MPLS label tables populated.
5. Linux kernel MPLS routes installed.
6. End-to-end ping successful.
7. MPLS encapsulation observed.

## Conclusion

The MPLS forwarding issue was resolved
by enabling Linux MPLS input processing.

Persistent Containerlab configuration
was added and successfully tested.

End-to-end MPLS transport verification
completed successfully.
