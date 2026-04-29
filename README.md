# 🏠 Home Lab — pfSense + Debian + Kali Linux

A virtual security lab built entirely from scratch on VMware Workstation.
Configured a pfSense firewall, a minimal Debian Linux server, and a Kali Linux
attack machine — all connected through an isolated private virtual network.

Ein virtuelles Sicherheitslabor, das von Grund auf mit VMware Workstation
aufgebaut wurde. pfSense-Firewall, minimaler Debian-Server und Kali Linux
als Angriffsmaschine — alle über ein isoliertes privates Netzwerk verbunden.

> ⏱️ Total setup time: ~9 hours | 9 documented errors | 0 tutorials followed step-by-step

---

## Why I Built This / Warum ich das gebaut habe

I wanted to learn how real network security environments work — not from watching videos, but from actually breaking things and fixing them myself. Every error in this lab taught me something a tutorial never would.

Ich wollte verstehen, wie echte Netzwerksicherheitsumgebungen funktionieren — nicht durch Videos, sondern durch echtes Ausprobieren. Jeder Fehler in diesem Lab hat mir etwas beigebracht, das kein Tutorial ersetzen kann.

---

## Network Architecture / Netzwerkarchitektur

```
[ Kali Linux ]──────┐
                    ├──[ pfSense Firewall ]──[ VMware NAT ]──[ Internet ]
[ Debian Server ]───┘
     (LAN: 10.10.10.x — isolated virtual network)
```

> 📸 See `network-diagram.png` for the full visual layout.

**Traffic flow:** All traffic from Kali and Debian passes through pfSense before reaching the internet. pfSense controls routing, firewall rules, and DHCP for the internal network.

---

## What Each Machine Does / Aufgaben der Maschinen

### 🔴 pfSense (Firewall / Router)
- Acts as the gateway between the internal lab network and the outside
- Handles DHCP — assigns IPs to Kali and Debian automatically
- Firewall rules control what traffic is allowed in and out
- Chosen because it's what real enterprise networks use

### 🟢 Debian Linux (Server)
- Installed as **minimal** — no GUI, no unnecessary packages
- Deliberate choice: a leaner system is faster, has a smaller attack surface, and forces you to learn the terminal properly
- Everything installed manually via `apt` from the command line
- Runs SSH so it can be accessed remotely from Kali

### 🔵 Kali Linux (Attack Machine)
- Used to simulate attacks against the Debian server in a safe, isolated environment
- Kept on the same virtual network as Debian, both behind pfSense

---

## Key Decisions & Why / Wichtige Entscheidungen

| Decision | Reason |
|----------|--------|
| Minimal Debian install | Smaller attack surface, forces real terminal usage |
| pfSense over a simple virtual switch | Real firewall experience, not just VM networking |
| Isolated LAN subnet (`192.168.100.x`) | Avoids IP conflicts with home router (`192.168.1.x`) |
| Separate troubleshooting log | Professional habit — document everything |

---

## What Went Wrong (The Honest Part) / Was schiefgelaufen ist

This lab did not go smoothly — and that's the point.

**9 real errors** were encountered and documented, including:
- OpenSSH completely breaking after VMware pause/resume (corrupted service state)
- Kali Linux terminal refusing to recognise any command (broken `$PATH`) — required full reinstall
- IP address conflicts when moving the lab to a different network
- CDROM entries in `sources.list` silently breaking every `apt update`
- A filename typo (`resources.list` vs `sources.list`) that took longer than it should have

Every error is documented in [`troubleshooting.md`](./troubleshooting.md) with the root cause, fix, and lesson learned.

---

## What I Learned / Was ich gelernt habe

- How a firewall sits between networks and controls traffic flow
- How DHCP assigns addresses and what happens when subnets conflict
- How minimal Linux systems work — nothing is installed by default, everything is intentional
- How `sources.list` controls package management in Debian
- How SSH works and what breaks it
- That knowing **when to reinstall vs when to keep debugging** is a real skill
- How to write proper troubleshooting documentation

---

## Tools & Stack

| Tool | Version | Role |
|------|---------|------|
| VMware Workstation | 17 | Hypervisor |
| pfSense | CE 2.7.x | Firewall / Router |
| Debian | Trixie (minimal) | Server |
| Kali Linux | 2024.x | Attack machine |

---

## Files in This Repo

```
home-lab/
├── README.md              ← You are here
├── troubleshooting.md     ← 9 errors, root causes, fixes, lessons
└── network-diagram.png    ← Visual layout of the full setup
```

---

*This is a living project. More configurations, experiments, and documentation will be added as the lab grows.*

![Homelab Diagram](homelab.png)