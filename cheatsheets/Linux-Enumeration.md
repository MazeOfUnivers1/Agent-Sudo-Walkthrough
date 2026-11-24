# Linux Enumeration Cheat Sheet

## Overview

Post-authentication enumeration on a Linux target focuses on identifying misconfigurations, weak permissions, and vulnerable software versions that can lead to privilege escalation. This engagement's toolkit included `LinEnum.sh` and `linpeas.sh`, and the manual commands below (`sudo -l`, filesystem checks) were directly used in [07-Privilege-Escalation](../docs/07-Privilege-Escalation/README.md).

## Common Syntax

```bash
sudo -l
id
uname -a
```

## Useful Commands

| Command | Description |
|---|---|
| `id` | Show current user, group, and group memberships |
| `whoami` | Show current effective username |
| `uname -a` | Show kernel version and architecture |
| `sudo -l` | List commands the current user may run via sudo |
| `sudo -V` | Show the installed sudo version |
| `cat /etc/passwd` | List all local user accounts |
| `find / -perm -4000 -type f 2>/dev/null` | Find SUID binaries |
| `find / -writable -type d 2>/dev/null` | Find world-writable directories |
| `ls -la /home/*` | Check other users' home directories for readable files |
| `crontab -l` | List current user's cron jobs |
| `cat /etc/crontab` | View system-wide cron jobs |
| `./LinEnum.sh` | Run automated Linux enumeration script |
| `./linpeas.sh` | Run automated Linux privilege escalation enumeration script |

## Important Options

| Option | Description |
|---|---|
| `sudo -l` | The single most important command — reveals what the user can already run as another user |
| `find / -perm -4000` | Locates SUID binaries, a classic privilege escalation vector |
| `getcap -r / 2>/dev/null` | Locates binaries with elevated Linux capabilities set |

## Examples

Sequence used in this engagement immediately after SSH login:

```bash
cd ..
ls
cd root       # Permission denied — confirms non-root context
pwd
sudo -l       # Reveals (ALL, !root) /bin/bash rule
sudo -V       # Confirms sudo 1.8.21p2 — vulnerable to CVE-2019-14287
```

## Common Mistakes

- Skipping `sudo -l` early in enumeration — it is one of the fastest ways to identify a direct privilege escalation path.
- Running automated scripts (LinEnum/linpeas) without also manually reviewing sudo permissions and running processes, since scripts can produce large amounts of output that bury key findings.
- Not checking the exact version of privileged binaries (like `sudo`) before assuming a misconfiguration is or isn't exploitable.

## References

- [LinEnum GitHub Repository](https://github.com/rebootuser/LinEnum)
- [PEASS-ng / linpeas GitHub Repository](https://github.com/carlospolop/PEASS-ng)
- [GTFOBins — Unix binaries for privilege escalation](https://gtfobins.github.io/)

## Key Takeaways

- `sudo -l` should be one of the first commands run after obtaining any shell on a Linux target.
- Always cross-reference the exact version of privileged binaries against known CVEs, as demonstrated with sudo 1.8.21p2 and CVE-2019-14287.
