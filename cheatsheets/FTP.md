# FTP Cheat Sheet

## Overview

FTP (File Transfer Protocol) is a plaintext protocol used for transferring files between a client and server, running by default on TCP port 21. It is frequently targeted in assessments due to weak/default credentials or anonymous access misconfigurations.

## Common Syntax

```bash
ftp <target>
hydra -l <user> -P <wordlist> <target> ftp
```

## Useful Commands

| Command | Description |
|---|---|
| `ftp <target>` | Connect interactively to an FTP server |
| `ftp anonymous@<target>` | Attempt anonymous login |
| `hydra -l <user> -P <wordlist> <target> ftp` | Brute-force a single known username's password |
| `hydra -L <userlist> -P <wordlist> <target> ftp` | Brute-force both usernames and passwords |
| `get <file>` | Download a file (inside an active FTP session) |
| `put <file>` | Upload a file (inside an active FTP session) |
| `ls` / `dir` | List remote directory contents (inside an active FTP session) |

## Important Options

| Option | Description |
|---|---|
| `-l` | Single, known username |
| `-L` | Path to a username wordlist |
| `-P` | Path to a password wordlist |
| `-t` | Number of parallel tasks/connections |
| `-V` | Verbose — show each login attempt |

## Examples

Single-user password spray, as used in [04-FTP](../docs/04-FTP/README.md):

```bash
hydra -l chris -P /usr/share/wordlists/rockyou.txt 10.113.149.11 ftp
```

Testing anonymous login:

```bash
ftp 10.113.149.11
# Username: anonymous
# Password: <any/blank>
```

## Common Mistakes

- Not testing anonymous login before jumping straight to brute-forcing.
- Running an unbounded brute-force (`-L` + `-P` combined lists) when a username is already known — this dramatically increases attack time.
- Ignoring account lockout policies, which can trigger denial-of-service on the target service.

## References

- [Hydra GitHub Repository](https://github.com/vanhauser-thc/thc-hydra)
- [RFC 959 — File Transfer Protocol](https://www.rfc-editor.org/rfc/rfc959)

## Key Takeaways

- Always test anonymous FTP access first — it requires no brute-forcing and is a very common misconfiguration.
- When a specific username is already known (from web enumeration, OSINT, etc.), a single-user password spray with Hydra is far more efficient than a combined user/password attack.
