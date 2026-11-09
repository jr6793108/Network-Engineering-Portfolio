# Project 01 - Complete Rebuild Command Reference

## Objective

Rebuild and verify the four-router MPLS
service-provider network using Containerlab,
Docker, FRRouting, IS-IS, and LDP.

## Network Architecture

PE1 --- P1 --- P2 --- PE2

## Step 1 - Verify Prerequisites

docker --version
sudo containerlab version
ip -V
sudo tcpdump --version

Refer to:
01-Prerequisites.md

## Step 2 - Enter the Project Directory

cd ~/Network-Engineering-Portfolio/Project-01-Service-Provider-Network

## Step 3 - Verify Configuration Files

ls -lh topology/service-provider.clab.yml

for router in PE1 P1 P2 PE2; do
  echo "========== $router =========="
  sudo ls -lh "configs/$router/"
done

## Step 4 - Review the Network Topology

cat topology/service-provider.clab.yml

Verify:

PE1 --- P1 --- P2 --- PE2

Confirm that bind-mount source paths match
the location of this project on the host.

## Step 5 - Deploy the Network

sudo containerlab deploy \
  -t topology/service-provider.clab.yml

## Step 6 - Verify Containers

sudo containerlab inspect \
  -t topology/service-provider.clab.yml

Expected:

PE1, P1, P2, PE2 running.

## Step 7 - Verify IS-IS Adjacencies

for router in PE1 P1 P2 PE2; do
  echo "========== $router IS-IS =========="
  docker exec "clab-sp-routing-lab-$router" \
    vtysh -c "show isis neighbor"
done

## Step 8 - Verify LDP Sessions

for router in PE1 P1 P2 PE2; do
  echo "========== $router LDP =========="
  docker exec "clab-sp-routing-lab-$router" \
    vtysh -c "show mpls ldp neighbor"
done

## Step 9 - Verify MPLS Tables

for router in PE1 P1 P2 PE2; do
  echo "========== $router MPLS =========="
  docker exec "clab-sp-routing-lab-$router" \
    vtysh -c "show mpls table"
done

## Step 10 - Verify Kernel MPLS Routes

for router in PE1 P1 P2 PE2; do
  echo "========== $router KERNEL MPLS =========="
  docker exec "clab-sp-routing-lab-$router" \
    ip -f mpls route show
done

## Step 11 - Verify MPLS Input Settings

for router in PE1 P1 P2 PE2; do
  echo "========== $router SETTINGS =========="
  docker exec "clab-sp-routing-lab-$router" \
    sysctl net.mpls.platform_labels

  docker exec "clab-sp-routing-lab-$router" \
    sysctl net.mpls.conf.eth1.input
done

Expected:

platform_labels = 100000
eth1.input = 1

On P1 and P2, also verify eth2.input = 1.

## Step 12 - Verify End-to-End Connectivity

docker exec clab-sp-routing-lab-PE1 \
  ping -c 15 -I 10.255.0.11 10.255.0.12

docker exec clab-sp-routing-lab-PE2 \
  ping -c 15 -I 10.255.0.12 10.255.0.11

Expected:

0% packet loss.

## Step 13 - Capture MPLS Traffic

Create evidence directory:

mkdir -p evidence/mpls/rebuild-verification

Get P1 container PID:

P1_PID=$(docker inspect -f '{{.State.Pid}}' \
  clab-sp-routing-lab-P1)

Start capture in the background:

sudo timeout 20 nsenter -t "$P1_PID" -n \
  tcpdump -ni eth2 -e -vv \
  -w evidence/mpls/rebuild-verification/P1-MPLS.pcap \
  'ether proto 0x8847' &

CAPTURE_PID=$!

sleep 3

Generate traffic:

docker exec clab-sp-routing-lab-PE1 \
  ping -c 15 -i 0.5 \
  -I 10.255.0.11 10.255.0.12

wait "$CAPTURE_PID"

Decode capture:

sudo tcpdump -nn -e -vv \
  -r evidence/mpls/rebuild-verification/P1-MPLS.pcap \
  -c 3

Expected:

EtherType 0x8847
MPLS label present
ICMP traffic encapsulated

Note:
A timeout exit status of 124 is expected
when the capture reaches its time limit.

## Step 14 - Save Verification Evidence

mkdir -p evidence/mpls/rebuild-verification

for router in PE1 P1 P2 PE2; do
  container="clab-sp-routing-lab-$router"

  docker exec "$container" \
    vtysh -c "show isis neighbor" \
    > "evidence/mpls/rebuild-verification/$router-isis.txt"

  docker exec "$container" \
    vtysh -c "show mpls ldp neighbor" \
    > "evidence/mpls/rebuild-verification/$router-ldp.txt"

  docker exec "$container" \
    vtysh -c "show mpls table" \
    > "evidence/mpls/rebuild-verification/$router-mpls.txt"
done

## Step 15 - Final Validation

Confirm:

- Four containers running
- IS-IS adjacencies operational
- LDP sessions operational
- MPLS forwarding tables populated
- Linux MPLS routes installed
- End-to-end connectivity successful
- MPLS packets captured

## References

Containerlab:
https://containerlab.dev/

FRRouting:
https://docs.frrouting.org/en/stable-10.3/

Docker:
https://docs.docker.com/

Linux iproute2:
https://man7.org/linux/man-pages/man8/ip.8.html

tcpdump:
https://www.tcpdump.org/

## Conclusion

The service-provider MPLS network can be
reconstructed using the documented topology,
FRRouting configurations, and verification
procedures.
