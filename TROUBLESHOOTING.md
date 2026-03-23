\# Troubleshooting Log / Fehlerbehebungsprotokoll



> 9 real errors encountered during setup.

> Every error documented with root cause, fix, and lesson learned.

> Total setup time: \~9 hours (most spent on Errors 5-7).



\---



\## Error 1 — pfSense config lost after break



\*\*Symptom:\*\* Configuration forgotten after 3-day break.

\*\*Root cause:\*\* No documentation kept during initial setup.

\*\*Fix:\*\* Factory reset → Option 4 → reconfigure from scratch.

\*\*Lesson:\*\* Document every change immediately. Factory reset = 30 min. Notes = 2 min.



\---



\## Error 2 — Debian mirror unreachable during install



\*\*Symptom:\*\* `Bad archive mirror` during Debian installation.

\*\*Root cause:\*\* Slow internet could not reach mirror servers.

\*\*Fix:\*\* Selected `Continue without a network mirror`. Fixed mirror post-install.

\*\*Lesson:\*\* Mirror can always be configured after install from terminal.



\---



\## Error 3 — nano keyboard frozen in VMware



\*\*Symptom:\*\* nano open, keyboard not responding inside VM.

\*\*Root cause:\*\* VMware lost keyboard focus. Keys going to Windows host.

\*\*Fix:\*\* Click directly inside VM window → Ctrl+Alt to recapture focus.

\*\*Alternative:\*\* Use `echo "content" > file` instead of nano.

\*\*Lesson:\*\* Always click inside VM window before typing. echo > file avoids nano entirely.



\---



\## Error 4 — cat command misunderstood



\*\*Symptom:\*\* Typed `cat` then Enter, then filename, then EOF on separate lines.

\*\*Root cause:\*\* Misunderstood that `cat` with no arguments reads keyboard input.

\*\*What happened:\*\* cat printed everything back like a parrot. Nothing was written.

\*\*Fix:\*\* Commands and arguments go on ONE line: `cat /etc/apt/sources.list`

\*\*Lesson:\*\* Press Ctrl+C to exit a stuck command. One line = one command + all arguments.



\---



\## Error 5 — CDROM warning persisting after sources.list fix



\*\*Symptom:\*\* Every `apt-get update` showed CDROM error despite fixing sources.list.

\*\*Root cause:\*\* Debian installer automatically adds CDROM as package source.

\*\*Fix:\*\*

