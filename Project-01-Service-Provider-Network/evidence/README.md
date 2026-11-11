# Evidence inventory

The original evidence directory on the Ubuntu host is:

`topology/tests/full-recovery/evidence/post-recovery-20261111-002916/`

Earlier verification confirmed **11 evidence files** and that every entry in `SHA256SUMS` validated. The set included PE running configurations, VPNv4 routing tables, customer routing tables, BGP summaries, bidirectional CE ping results, and container status.

The final explicit-loopback ping results from Step 16O-351N were:

| Direction | Source | Destination | Sent | Received | Loss | Average RTT |
|---|---|---|---:|---:|---:|---:|
| CE1 → CE2 | `192.168.10.1` | `192.168.20.1` | 5 | 5 | 0% | 0.066 ms |
| CE2 → CE1 | `192.168.20.1` | `192.168.10.1` | 5 | 5 | 0% | 0.066 ms |

**Important:** This documentation package does not contain the actual evidence files from the Ubuntu VM. Copy the original evidence folder and optionally a new `final-loopback-connectivity.txt` transcript into your GitHub repository after checking for sensitive content. Do not represent this inventory as raw command output.
