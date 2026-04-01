# Wireshark

**What it is:** A network protocol analyzer (packet sniffer) that captures and inspects traffic in real time.

## Two Versions

| Version | How to launch | Best for |
|---------|--------------|----------|
| **Wireshark** (GUI) | `wireshark` | Visual analysis, learning, color-coded packets |
| **tshark** (CLI) | `tshark` | Scripting, remote servers, automated captures |

## What it Captures

- **TCP** — connection-based traffic (web, file transfers, SSH)
- **UDP** — connectionless traffic (DNS, video streaming, VoIP)
- **ARP** — maps IP addresses to MAC addresses on a local network
- **HTTP/HTTPS, DNS, ICMP** and hundreds of other protocols

## What You Can See
- Source and destination IP / MAC addresses
- Port numbers (which service is communicating)
- Full packet contents (unencrypted HTTP is readable as plaintext)
- Handshakes, authentication exchanges, and errors

## Common Display Filters

```
tcp                         # Show only TCP packets
udp                         # Show only UDP packets
arp                         # Show only ARP packets
http                        # HTTP traffic only
dns                         # DNS queries and responses
ip.addr == 192.168.1.1      # All traffic to/from a specific IP
ip.src == 192.168.1.1       # Only traffic FROM that IP
tcp.port == 80              # Traffic on port 80
```

## tshark Examples

```bash
tshark -i eth0              # Live capture on interface eth0
tshark -i eth0 -w out.pcap  # Save capture to a file
tshark -r capture.pcap      # Read and analyze a saved capture
tshark -i eth0 -f "tcp"     # Capture filter (only TCP)
```

> ⚠️ Capturing network traffic without authorization is illegal in most countries. Only sniff your own network or a controlled lab environment.
