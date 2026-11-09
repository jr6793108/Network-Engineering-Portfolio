# Project 01 - IP Addressing Plan

## Network Architecture

PE1 --- P1 --- P2 --- PE2

## Router Loopback Addresses

| Router | Loopback |
|--------|----------|
| PE1 | 10.255.0.11/32 |
| P1 | 10.255.0.1/32 |
| P2 | 10.255.0.2/32 |
| PE2 | 10.255.0.12/32 |

## Transport Interfaces

| Router | Interface | IP Address |
|--------|-----------|------------|
| PE1 | eth1 | 10.0.11.2/30 |
| P1 | eth2 | 10.0.11.1/30 |
| P1 | eth1 | 10.0.12.1/30 |
| P2 | eth1 | 10.0.12.2/30 |
| P2 | eth2 | 10.0.22.1/30 |
| PE2 | eth1 | 10.0.22.2/30 |

## Routing Protocol

IS-IS Level 2

Routing process: CORE

## MPLS

Label Distribution Protocol: LDP

MPLS platform label limit: 100000

## Purpose

This addressing plan supports an MPLS
service-provider transport network
with two provider-edge routers and
two provider-core routers.
