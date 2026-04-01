# Nmap (Network Mapper)

**What it is:** A free, open-source tool for network discovery and security auditing.

## What it does
- Discovers devices connected to a network
- Identifies open/closed/filtered ports on a target
- Detects services and their versions (e.g., Apache 2.4, OpenSSH 8.1)
- Detects the operating system of the target
- Maps out an entire network topology

## Common Flags

| Flag | Description |
|------|-------------|
| `-A` | **Aggressive scan** — OS detection, version detection, script scanning, and traceroute all at once |
| `-sV` | Service/version detection only |
| `-sS` | SYN stealth scan (default, less noisy) |
| `-p` | Specify ports (e.g., `-p 80,443` or `-p 1-1000`) |
| `-O` | OS detection only |
| `-T4` | Speed template (0=slowest, 5=fastest) |
| `-oN` | Save output to a normal text file |

## Examples

```bash
# Aggressive scan on a single target
nmap -A 192.168.1.1

# Service detection on port range across a subnet
nmap -sV -p 1-1000 192.168.1.0/24

# Save results to a file
nmap -A 192.168.1.1 -oN results.txt
```

> ⚠️ Only scan networks and devices you own or have explicit written permission to test.
