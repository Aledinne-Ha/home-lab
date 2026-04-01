# 🛡️ Cybersecurity Tools — Personal Notes

> **Status:** Learning journal — road to becoming an engineer  
> **Last updated:** April 2026

---

## 1. Nmap (Network Mapper)

**What it is:** A free, open-source tool used for network discovery and security auditing. It scans networks to find live hosts, open ports, running services, and operating system information.

**What it does:**
- Discovers devices connected to a network
- Identifies open/closed/filtered ports on a target
- Detects services and their versions (e.g., Apache 2.4, OpenSSH 8.1)
- Detects the operating system of the target
- Can map out an entire network topology

**Common flags:**

| Flag | Description |
|------|-------------|
| `-A` | **Aggressive scan** — enables OS detection, version detection, script scanning, and traceroute all at once |
| `-sV` | Service/version detection only |
| `-sS` | SYN stealth scan (default, less noisy) |
| `-p` | Specify ports (e.g., `-p 80,443` or `-p 1-1000`) |
| `-O` | OS detection |
| `-T4` | Speed template (0=slowest, 5=fastest) |
| `-oN` | Save output to a normal text file |

**Example:**
```bash
nmap -A 192.168.1.1
nmap -sV -p 1-1000 192.168.1.0/24
```

> ⚠️ Always scan only networks and devices you own or have explicit written permission to test.

---

## 2. Metasploit Framework

**What it is:** The world's most widely used penetration testing framework, maintained by Rapid7. It provides tools for developing, testing, and executing exploits against remote targets.

**What it does:**
- Scans and gathers detailed information about a target (reconnaissance)
- Identifies known vulnerabilities in services/software
- Executes exploits to gain access to vulnerable systems
- Can deliver payloads (e.g., reverse shells, Meterpreter sessions)
- Post-exploitation: escalate privileges, pivot through networks, extract data

**Key components:**

| Component | Description |
|-----------|-------------|
| `msfconsole` | Main interactive console to run Metasploit |
| `auxiliary` | Scanning and info-gathering modules (no exploitation) |
| `exploit` | Modules that attack a specific vulnerability |
| `payload` | Code that runs on the target after exploitation |
| `Meterpreter` | Advanced interactive shell on the compromised machine |

**Basic workflow:**
```bash
msfconsole                          # Launch Metasploit
search <service/vulnerability>      # Find a relevant module
use <module_path>                   # Select it
show options                        # See required settings
set RHOSTS <target_IP>              # Set target
run                                 # Execute
```

> ⚠️ Metasploit must only be used in legal lab environments (e.g., Metasploitable VMs, TryHackMe, HackTheBox) or with explicit authorization.

---

## 3. Wireshark

**What it is:** A network protocol analyzer (packet sniffer). It captures and inspects data traveling through a network interface in real time.

**Two versions:**
- **Wireshark (GUI):** Graphical interface, color-coded packets, filter bar, detailed protocol breakdown — best for analysis and learning
- **tshark (CLI/terminal):** Command-line version of Wireshark — useful for scripting, remote servers, or automated captures

**What it captures:**
- **TCP** (Transmission Control Protocol) — connection-based data (web traffic, file transfers)
- **UDP** — connectionless traffic (DNS, video streaming)
- **ARP** (Address Resolution Protocol) — maps IP addresses to MAC addresses on a local network
- **HTTP/HTTPS, DNS, ICMP**, and hundreds of other protocols

**What you can see:**
- Source and destination IP/MAC addresses
- Port numbers (which service/app is communicating)
- Packet contents (unencrypted traffic like HTTP can be read in plaintext)
- Handshakes, authentication exchanges, errors

**Common filters:**
```
tcp                        # Show only TCP packets
arp                        # Show only ARP packets
ip.addr == 192.168.1.1     # Traffic to/from a specific IP
http                       # HTTP traffic only
tcp.port == 80             # Traffic on port 80
```

**tshark example:**
```bash
tshark -i eth0             # Capture on interface eth0
tshark -r capture.pcap     # Read from a saved capture file
```

> ⚠️ Capturing network traffic without authorization is illegal in most countries. Only sniff your own network or a lab environment.

---

## 4. Brute Force Attacks

**What it is:** An attack method that systematically tries every possible combination of credentials (usernames, passwords, PINs) until the correct one is found.

**How it works:**
- Uses a **wordlist** (dictionary file) — a text file containing thousands or millions of common passwords/usernames
- The most famous wordlist is **`rockyou.txt`** — a real leaked password database with ~14 million entries, commonly used in CTFs and pentesting labs
- Tools try each entry from the wordlist against the target login

**Common brute force tools:**

| Tool | Common Use Case |
|------|----------------|
| **Hydra** | Online brute force — SSH, FTP, HTTP login forms, RDP |
| **John the Ripper** | Offline password hash cracking |
| **Hashcat** | GPU-accelerated hash cracking (extremely fast) |
| **Medusa** | Similar to Hydra, multi-protocol online brute force |

**Hydra example (SSH):**
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.1
```
- `-l` = single username to try
- `-L` = file with list of usernames
- `-p` = single password
- `-P` = wordlist file path

**Wordlist location (Kali Linux):**
```bash
/usr/share/wordlists/rockyou.txt
# (may need to unzip: gunzip rockyou.txt.gz)
```

**Types of brute force:**
- **Dictionary attack** — uses a wordlist (most common)
- **Pure brute force** — tries every character combination (very slow, used for short/simple targets)
- **Credential stuffing** — uses leaked username:password pairs from data breaches

> ⚠️ Brute forcing systems without permission is a criminal offense. Practice only on intentionally vulnerable machines (e.g., DVWA, TryHackMe labs).

---

## 🗺️ Tools Summary

| Tool | Category | Main Purpose |
|------|----------|-------------|
| Nmap | Reconnaissance | Network scanning, port/service/OS discovery |
| Metasploit | Exploitation | Vulnerability exploitation & post-exploitation |
| Wireshark / tshark | Traffic Analysis | Packet capture and protocol inspection |
| Hydra / John / Hashcat | Credential Attacks | Brute forcing passwords online or offline |

---

## 📚 Recommended Next Steps

- [ ] Try **TryHackMe** (beginner-friendly guided labs)
- [ ] Set up a **Kali Linux VM** as your main pentesting environment
- [ ] Practice Nmap on your local network (your own devices)
- [ ] Use **Metasploitable 2** as a vulnerable target VM
- [ ] Complete a full Wireshark capture analysis on a CTF challenge
- [ ] Run Hydra against a local test machine (never live targets!)
