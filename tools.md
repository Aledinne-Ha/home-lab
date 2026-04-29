# Tools / Werkzeuge

Security tools studied and used during home lab setup and experiments.
Each tool includes what it does, how I used it in the lab, and the key commands.

Sicherheitstools, die während des Home-Lab-Aufbaus und bei Experimenten eingesetzt wurden.
Jedes Tool enthält eine Beschreibung, wie ich es im Labor verwendet habe, und die wichtigsten Befehle.

> ⚠️ **Ethics / Ethik:** Every tool on this page was used only against machines I own inside my private lab network. Using these tools against systems without explicit written permission is illegal in most countries. / Alle Tools auf dieser Seite wurden nur gegen eigene Maschinen im privaten Labornetzwerk verwendet. Der Einsatz dieser Tools gegen fremde Systeme ohne ausdrückliche schriftliche Genehmigung ist in den meisten Ländern illegal.

---

## 1. Nmap (Network Mapper)

**What it is:** Open-source tool for network discovery and security auditing. The first tool most security professionals run against any target.

**How I used it in the lab:**
> *Used against my Debian server (192.168.100.10) from Kali to verify which ports were open after configuring SSH. Confirmed port 22 was visible and responding. Also scanned the full lab subnet to see how pfSense appeared from the inside.*

### Common Flags

| Flag | Description |
|------|-------------|
| `-A` | Aggressive scan — OS detection, version detection, scripts, traceroute |
| `-sV` | Service and version detection only |
| `-sS` | SYN stealth scan (default, less noisy) |
| `-p` | Specify ports (e.g. `-p 22,80` or `-p 1-1000`) |
| `-O` | OS detection only |
| `-T4` | Speed template (0=slowest, 5=fastest) |
| `-oN` | Save output to a text file |

### Examples

```bash
# Aggressive scan on Debian server
nmap -A 192.168.100.10

# Scan full lab subnet for open ports
nmap -sV -p 1-1000 192.168.100.0/24

# Save results to file
nmap -A 192.168.100.10 -oN debian_scan.txt
```

---

## 2. Wireshark

**What it is:** A network protocol analyzer that captures and inspects traffic in real time. Lets you see exactly what is moving across your network at the packet level.

**How I used it in the lab:**
> Opened Wireshark on Kali and observed live traffic on the lab network. Not used extensively yet — will update when used in a future experiment.

### Two Versions

| Version | Launch | Best for |
|---------|--------|----------|
| **Wireshark** (GUI) | `wireshark` | Visual analysis, learning, color-coded packets |
| **tshark** (CLI) | `tshark` | Scripting, remote servers, automated captures |

### What It Captures

- **TCP** — connection-based traffic (web, SSH, file transfers)
- **UDP** — connectionless traffic (DNS, video, VoIP)
- **ARP** — maps IP addresses to MAC addresses on local network
- **HTTP, DNS, ICMP** and hundreds of other protocols

### Common Display Filters

```
tcp                         # TCP packets only
udp                         # UDP packets only
arp                         # ARP packets only
http                        # HTTP traffic only
dns                         # DNS queries and responses
ip.addr == 192.168.100.10   # All traffic to/from Debian server
ip.src == 192.168.100.30    # Traffic originating from Kali
tcp.port == 22              # SSH traffic only
```

### tshark Examples

```bash
tshark -i eth0                      # Live capture on eth0
tshark -i eth0 -w capture.pcap      # Save capture to file
tshark -r capture.pcap              # Read a saved capture
tshark -i eth0 -f "tcp port 22"     # Capture only SSH traffic
```

---

## 3. Hydra (Brute Force)

**What it is:** An online password brute forcing tool. Automatically tries thousands of passwords against a live service until one works.

**How I used it in the lab:**
> Not used in this lab yet — the lab was deleted before I got to this stage. Documented for reference and will be used in a future experiment when the lab is rebuilt.

### How Brute Force Works

1. Takes a **wordlist** — a `.txt` file with thousands or millions of common passwords
2. Tries each entry against the target login automatically
3. Stops when it finds a match

### The Most Important Wordlist

**`rockyou.txt`** — a real leaked password database with ~14 million entries. Included in Kali Linux by default.

```bash
# Location on Kali
/usr/share/wordlists/rockyou.txt

# If compressed, unzip first
gunzip /usr/share/wordlists/rockyou.txt.gz
```

### Key Flags

| Flag | Description |
|------|-------------|
| `-l` | Single username |
| `-L` | File with list of usernames |
| `-p` | Single password |
| `-P` | Wordlist file |
| `-t` | Parallel threads (e.g. `-t 4`) |
| `-v` | Verbose output |

### Examples

```bash
# Brute force SSH with known username
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.100.10

# Brute force with username list
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.100.10

# Brute force HTTP login form
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.100.10 http-post-form "/login:username=^USER^&password=^PASS^:Invalid"
```

### Types of Brute Force

| Type | Description |
|------|-------------|
| **Dictionary attack** | Uses a wordlist — fast and most common |
| **Pure brute force** | Tries every possible character combination — very slow |
| **Credential stuffing** | Uses real leaked username:password pairs from breaches |

---

## 4. Metasploit Framework

**What it is:** The world's most widely used penetration testing framework. Maintained by Rapid7. Goes beyond scanning — it actually exploits vulnerabilities and gives you access to compromised systems.

**How I used it in the lab:**
> Not used in this lab yet — the lab was deleted before I got to this stage. Documented for reference and will be used in a future experiment when the lab is rebuilt.

### What It Does

- Scans and gathers information about targets (reconnaissance)
- Identifies known vulnerabilities in running services
- Executes exploits against vulnerable systems
- Delivers payloads — reverse shells, Meterpreter sessions
- Post-exploitation: privilege escalation, pivoting, data extraction

### Key Components

| Component | Description |
|-----------|-------------|
| `msfconsole` | Main interactive console — where everything happens |
| `auxiliary` | Scanning and info-gathering modules (no exploitation) |
| `exploit` | Modules that attack a specific known vulnerability |
| `payload` | Code that executes on the target after exploitation |
| `Meterpreter` | Advanced interactive shell on a compromised machine |

### Basic Workflow

```bash
msfconsole                           # Launch Metasploit
search <service or vulnerability>    # Find a relevant module
use <module_path>                    # Select it
show options                         # See what needs to be configured
set RHOSTS 192.168.100.10            # Set target (Debian server in lab)
run                                  # Execute
```

### Useful Commands Inside msfconsole

```bash
db_nmap -A 192.168.100.10    # Run Nmap and save results to Metasploit DB
hosts                         # Show all discovered hosts
services                      # Show discovered services
vulns                         # Show identified vulnerabilities
sessions -l                   # List active sessions
sessions -i 1                 # Interact with session 1
```

---

## Tool Comparison / Werkzeugvergleich

| Tool | Category | Online/Offline | Primary Use |
|------|----------|---------------|-------------|
| **Nmap** | Reconnaissance | Online | Discover hosts, ports, services |
| **Wireshark** | Traffic Analysis | Online | Capture and inspect packets |
| **Hydra** | Exploitation | Online | Brute force live login services |
| **Metasploit** | Exploitation | Online | Full exploitation framework |

---

## What I Learned from These Tools / Was ich gelernt habe

- How to approach a target methodically: recon first (Nmap) → analyze traffic (Wireshark) → attempt access (Hydra/Metasploit)
- That security tools are only as useful as your understanding of what they output
- The difference between a port being **open**, **closed**, and **filtered**
- What a real network handshake looks like at the packet level
- Why weak passwords are catastrophically dangerous when tools like Hydra exist
- That every professional tool comes with serious legal and ethical responsibility

---

*All experiments performed inside an isolated lab network (VMnet2 — 192.168.100.0/24) with no connection to external systems.*
