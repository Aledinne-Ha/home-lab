# Troubleshooting Log / Fehlerbehebungsprotokoll

9+ real errors encountered during setup.
Every error documented with root cause, fix, and lesson learned.
Total setup time: ~9 hours (most spent on Errors 5–7).

---

## Error 1 — pfSense config lost after break
**Symptom:** Configuration forgotten after 3-day break.
**Root cause:** No documentation kept during initial setup.
**Fix:** Factory reset → Option 4 → reconfigure from scratch.
**Lesson:** Document every change immediately. Factory reset = 30 min. Notes = 2 min.

---

## Error 2 — Debian mirror unreachable during install
**Symptom:** `Bad archive mirror` during Debian installation.
**Root cause:** Slow internet could not reach mirror servers during setup wizard.
**Fix:** Selected `Continue without a network mirror`. Fixed mirror post-install via terminal.
**Lesson:** Mirror can always be configured after install. Don't let one failure block the whole setup.

---

## Error 3 — nano keyboard frozen in VMware
**Symptom:** nano open, keyboard not responding inside VM.
**Root cause:** VMware lost keyboard focus — keystrokes going to Windows host instead of VM.
**Fix:** Click directly inside VM window → Ctrl+Alt to recapture focus.
**Alternative:** Use `echo "content" > file` instead of nano when focus issues persist.
**Lesson:** Always click inside VM window before typing. `echo > file` avoids nano entirely.

---

## Error 4 — cat command misunderstood
**Symptom:** Typed `cat` then Enter, then filename on a separate line — terminal just echoed everything back.
**Root cause:** `cat` with no arguments reads from keyboard input (stdin), not a file.
**Fix:** Commands and arguments go on ONE line: `cat /etc/apt/sources.list`
**Lesson:** Press Ctrl+C to exit any stuck command. One line = one command + all its arguments.

---

## Error 5 — sources.list named incorrectly (typo)
**Symptom:** `apt-get update` failed with unrecognised source file error.
**Root cause:** Created the file as `resources.list` instead of `sources.list` — one wrong word and apt ignores it entirely.
**Fix:** Renamed the file to the correct name:
```bash
mv /etc/apt/resources.list /etc/apt/sources.list
```
**Lesson:** Linux is exact. One wrong character in a filename and the whole system ignores the file silently. Always double-check filenames against documentation.

---

## Error 6 — CDROM warning persisting after sources.list fix
**Symptom:** Every `apt-get update` showed a CDROM error even after fixing sources.list.
**Root cause:** Debian's installer automatically adds the installation CDROM as a package source. It stays there until manually removed.
**Fix:** Opened sources.list and commented out or deleted the CDROM line:
```bash
nano /etc/apt/sources.list
# Comment out or delete this line:
# deb cdrom:[Debian...]/ bookworm main
```
Then rewrote the file with correct mirror sources and ran:
```bash
apt-get update
```
**Lesson:** Always inspect sources.list fully after a minimal Debian install. The CDROM entry is added automatically and will break updates if left in.

---

## Error 7 — OpenSSH broken after VMware pause/resume
**Symptom:** SSH completely non-functional the day after pausing the VM. Service wouldn't start, connections refused.
**Root cause:** VMware's pause/resume feature suspends the OS mid-process. OpenSSH's runtime state (socket files, PID files, service state) gets corrupted on resume because the OS didn't shut down cleanly.
**Fix:** Reinstalled OpenSSH from scratch:
```bash
apt-get remove --purge openssh-server
apt-get install openssh-server
systemctl enable ssh
systemctl start ssh
systemctl status ssh
```
**Lesson:** Never pause a VM mid-session if services are running. Either snapshot it or do a clean shutdown (`shutdown -h now`). Pause is not the same as shutdown — the OS doesn't know time passed.

---

## Error 8 — Kali Linux terminal broken, no commands recognised
**Symptom:** Kali terminal not recognising any commands at all — even basic ones like `ls` or `cd` returned errors.
**Root cause:** `$PATH` environment variable corrupted or shell configuration files (`.bashrc` / `.zshrc`) broken. When PATH breaks, the shell cannot locate any executable, making the entire terminal unusable.
**Fix attempted:** Searched for hours — tried resetting PATH manually, reconfiguring shell, reinstalling packages. No solution worked.
**Final fix:** Fresh reinstall of Kali Linux.
**Lesson:** Some things are faster to reinstall than to fix. A broken shell environment can cascade into every part of the system. Knowing when to cut losses and reinstall is a real skill, not a failure. Snapshots before major config changes would have saved hours.

---

## Error 9 — IP address conflict when taking lab outside home network
**Symptom:** After moving the lab to a different network (outside home), VMs had no connectivity or conflicting IPs. pfSense and the host network were fighting over addresses.
**Root cause:** pfSense was configured with a LAN subnet that overlapped with the new external network's subnet. Both networks were trying to use the same IP range — the machines couldn't tell which network was which.
**Fix:** Reconfigured pfSense LAN interface to use a non-conflicting private subnet (e.g. changed from `192.168.1.x` to `10.10.10.x`) so it no longer overlapped with the external router.
**Lesson:** Always use an uncommon private subnet for your lab LAN (`10.x.x.x` ranges are safer than `192.168.1.x` which every home router defaults to). Network conflicts are invisible until you change environments — plan for portability from the start.

---

## Summary / Zusammenfassung

| Error | Category | Time Lost |
|-------|----------|-----------|
| 1 — pfSense config lost | Documentation | ~30 min |
| 2 — Mirror unreachable | Installation | ~15 min |
| 3 — VMware keyboard focus | VM Management | ~10 min |
| 4 — cat misunderstood | Linux basics | ~10 min |
| 5 — sources.list typo | File naming | ~20 min |
| 6 — CDROM in sources | Package management | ~45 min |
| 7 — OpenSSH broken | Service management | ~1.5 hrs |
| 8 — Kali terminal broken | Shell/PATH corruption | ~3 hrs |
| 9 — IP conflict | Networking | ~1 hr |

**Total: ~9 hours. Every hour taught something that a tutorial never would.**
