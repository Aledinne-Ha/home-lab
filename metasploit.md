# Metasploit Framework

**What it is:** The world's most widely used penetration testing framework, maintained by Rapid7.

## What it does
- Scans and gathers detailed information about a target (reconnaissance)
- Identifies known vulnerabilities in services/software
- Executes exploits to gain access to vulnerable systems
- Delivers payloads (e.g., reverse shells, Meterpreter sessions)
- Post-exploitation: escalate privileges, pivot through networks, extract data

## Key Components

| Component | Description |
|-----------|-------------|
| `msfconsole` | Main interactive console |
| `auxiliary` | Scanning and info-gathering modules (no exploitation) |
| `exploit` | Modules that attack a specific vulnerability |
| `payload` | Code that runs on the target after exploitation |
| `Meterpreter` | Advanced interactive shell on the compromised machine |

## Basic Workflow

```bash
msfconsole                          # Launch Metasploit
search <service/vulnerability>      # Find a relevant module
use <module_path>                   # Select it
show options                        # See required settings
set RHOSTS <target_IP>              # Set target
run                                 # Execute
```

## Useful Commands Inside msfconsole

```bash
db_nmap -A 192.168.1.1    # Run Nmap scan and save results to Metasploit DB
hosts                      # Show discovered hosts
services                   # Show discovered services
vulns                      # Show found vulnerabilities
sessions -l                # List active sessions
sessions -i 1              # Interact with session 1
```

> ⚠️ Only use Metasploit in legal lab environments (e.g., Metasploitable VM, TryHackMe, HackTheBox) or with explicit written authorization.
