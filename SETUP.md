# Setup Guide / Einrichtungsanleitung

## Requirements / Voraussetzungen

| Component | Version | Notes |
|---|---|---|
| VMware Workstation Pro | Latest | Free for personal use |
| pfSense ISO | 2.7.x | AMD64, DVD Image |
| Debian ISO | 13 Trixie | netinst with firmware |
| Kali Linux ISO | Latest | 64-bit Installer |
| RAM | 16GB recommended | 8GB minimum |
| Disk space | 80GB free | For all VMs |

---

## Step 1 — Create VMnet2 (Internal Lab Network) / VMnet2 erstellen (Internes Labornetzwerk)

```
VMware → Edit → Virtual Network Editor
→ Add Network → VMnet2
→ Type: Host-only
→ Uncheck: Use local DHCP service
→ Subnet IP: 192.168.100.0
→ Subnet mask: 255.255.255.0
→ Apply → OK
```

This creates a private isolated network for lab VMs only.
No traffic from this network reaches the real internet directly.
All internet access goes through pfSense.

Dies erstellt ein privates, isoliertes Netzwerk nur für Labor-VMs.
Kein Datenverkehr aus diesem Netzwerk erreicht das echte Internet direkt.
Der gesamte Internetzugang läuft über pfSense.

---

## Step 2 — Install pfSense (Firewall) / pfSense installieren (Firewall)

### VM Settings / VM-Einstellungen

```
RAM: 512 MB
Disk: 8 GB (single file)
Network Adapter 1: NAT        ← WAN (internet / Internet)
Network Adapter 2: VMnet2     ← LAN (lab network / Labornetzwerk)
```

### Installation

```
Boot from ISO → Enter → Accept → Install pfSense
→ Auto (ZFS) → Proceed → Stripe → select da0 → YES
→ Wait ~5 minutes → Reboot
→ Disconnect ISO before reboot / ISO vor dem Neustart trennen
```

### Interface Configuration / Schnittstellenkonfiguration

```
Option 1 — Assign Interfaces / Schnittstellen zuweisen:
    VLANs? → n
    WAN interface → em0
    LAN interface → em1
    Proceed? → y

Option 2 — Set LAN IP / LAN-IP festlegen:
    Configure via DHCP? → n
    New LAN IP → 192.168.100.1
    Subnet bits → 24
    Gateway → (Enter — none / keine)
    IPv6 → (Enter — none / keine)
    Enable DHCP? → y
    Start → 192.168.100.50
    End → 192.168.100.200
    Revert HTTP? → n
```

### Verify / Überprüfen

```
Console shows / Konsole zeigt:
    WAN: 10.0.2.x
    LAN: 192.168.100.1
```

---

## Step 3 — Install Debian Server / Debian Server installieren

### VM Settings / VM-Einstellungen

```
RAM: 1024 MB
Disk: 20 GB (single file)
Network Adapter: VMnet2     ← lab network only / nur Labornetzwerk
```

### Installation

```
Text Install (not Graphical / nicht Grafisch)
Hostname: debian-lab
Domain: (blank / leer lassen)
Root password: (set one / eines festlegen)
Username: admin
Password: (set one / eines festlegen)
Partitioning: Guided → entire disk → all files in one partition
Software: SSH server + standard system utilities ONLY
GRUB: Yes → /dev/sda
```

### Post-Install Configuration / Konfiguration nach der Installation

Fix package sources / Paketquellen korrigieren:
```bash
echo "deb http://deb.debian.org/debian trixie main contrib non-free" > /etc/apt/sources.list
echo "deb http://deb.debian.org/debian trixie-updates main contrib non-free" >> /etc/apt/sources.list
echo "deb http://security.debian.org/debian-security trixie-security main contrib non-free" >> /etc/apt/sources.list
apt-get update
```

Install SSH / SSH installieren:
```bash
apt-get install openssh-server -y
systemctl start ssh
systemctl enable ssh
```

Enable root SSH login / Root-SSH-Login aktivieren:
```bash
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
systemctl restart ssh
```

Set static IP / Statische IP festlegen:
```bash
echo "auto ens33" > /etc/network/interfaces
echo "iface ens33 inet static" >> /etc/network/interfaces
echo "  address 192.168.100.10" >> /etc/network/interfaces
echo "  netmask 255.255.255.0" >> /etc/network/interfaces
echo "  gateway 192.168.100.1" >> /etc/network/interfaces
echo "  dns-nameservers 8.8.8.8" >> /etc/network/interfaces
systemctl restart networking
```

### Verify / Überprüfen

```bash
ip addr show ens33
# Should show / Sollte anzeigen: 192.168.100.10

ping 8.8.8.8 -c 3
# Should get replies / Sollte Antworten erhalten
```

---

## Step 4 — Install Kali Linux / Kali Linux installieren

### VM Settings / VM-Einstellungen

```
RAM: 2048 MB
Processors: 2 cores / 2 Kerne
Disk: 40 GB (single file)
Network Adapter: VMnet2
```

### Installation

```
Graphical Install / Grafische Installation
Language: English
Hostname: kali-lab
Username: kali
Software: Kali desktop (XFCE) + Top 10 tools + standard utilities
GRUB: Yes → /dev/sda
```

### Post-Install Configuration / Konfiguration nach der Installation

Set static IP / Statische IP festlegen:
```bash
sudo nano /etc/network/interfaces
```

Add below existing lo configuration / Unterhalb der bestehenden lo-Konfiguration hinzufügen:
```
auto eth0
iface eth0 inet static
    address 192.168.100.30
    netmask 255.255.255.0
    gateway 192.168.100.1
    dns-nameservers 8.8.8.8
```

Apply changes / Änderungen anwenden:
```bash
sudo systemctl restart networking
ip addr show
```

---

## Step 5 — Verify Full Lab Connectivity / Vollständige Laborkonnektivität überprüfen

From Kali terminal / Vom Kali-Terminal:
```bash
ping 192.168.100.1 -c 3     # pfSense gateway — must work / muss funktionieren
ping 192.168.100.10 -c 3    # Debian server — must work / muss funktionieren
ping 8.8.8.8 -c 3           # Internet through pfSense — must work / muss funktionieren
ssh root@192.168.100.10     # SSH to Debian — must connect / muss verbinden
```

From browser on Kali / Vom Browser auf Kali:
```
http://192.168.100.1
Login: admin / pfsense
```

All checks passing = lab fully operational.
Alle Überprüfungen bestanden = Labor voll betriebsbereit.

---

## Network Architecture / Netzwerkarchitektur

```
[Internet]
    ↓
[pfSense Firewall]
    WAN: 10.0.2.x  (VMware NAT)
    LAN: 192.168.100.1/24
        ↓
[Lab Network — VMnet2: 192.168.100.0/24]
    ├── Debian Server  → 192.168.100.10
    └── Kali Linux     → 192.168.100.30
```

| VM | OS | IP | RAM | Role / Rolle |
|---|---|---|---|---|
| pfSense | FreeBSD | 192.168.100.1 | 512MB | Firewall / Gateway |
| Debian Server | Debian 13 Trixie | 192.168.100.10 | 1GB | Linux Server |
| Kali Linux | Kali Linux | 192.168.100.30 | 2GB | Attack / Admin |

# Setup Guide — Changelog

- 2026-04-01: Added Debian 13 and Kali installation steps, pfSense configuration
- 2026-03-21: Initial draft with network setup only