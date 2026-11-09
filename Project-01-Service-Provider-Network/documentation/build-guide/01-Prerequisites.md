# Project 01 - Service Provider Network

## Phase 1 - Prerequisites

### Objective

Build a four-router service-provider network
using Containerlab, Docker, and FRRouting.

### Network Topology

PE1 --- P1 --- P2 --- PE2

### Technology Stack

- Ubuntu Linux
- Docker Engine
- Containerlab
- FRRouting 10.3.1
- IS-IS Level 2
- MPLS
- Label Distribution Protocol (LDP)
- tcpdump

### Required Host Tools

1. Docker
2. Containerlab
3. Git
4. tcpdump
5. iproute2
6. A text editor

### Verification Commands

Check Ubuntu:

cat /etc/os-release

Check Docker:

docker --version

Check Containerlab:

containerlab version

Check Git:

git --version

Check tcpdump:

tcpdump --version

### Expected Results

All required tools should be installed
and accessible from the Ubuntu terminal.

### References

Docker:
https://docs.docker.com/engine/install/ubuntu/

Containerlab:
https://containerlab.dev/manual/

FRRouting:
https://docs.frrouting.org/en/stable-10.3/

Linux MPLS:
https://docs.kernel.org/networking/mpls-sysctl.html

