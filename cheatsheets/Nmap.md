# Nmap Cheat Sheet

## Overview

Nmap ("Network Mapper") is a network scanning tool used to discover hosts, open ports, running services, service versions, and operating systems on a network.

## Common Syntax

```bash
nmap [scan type] [options] <target>
```

## Useful Commands

| Command | Description |
|---|---|
| `nmap <target>` | Basic scan of the top 1000 ports |
| `nmap -p- <target>` | Scan all 65535 TCP ports |
| `nmap -sV <target>` | Detect service versions on open ports |
| `nmap -sC <target>` | Run default NSE (Nmap Scripting Engine) scripts |
| `nmap -O <target>` | Attempt OS detection |
| `nmap -A <target>` | Enable OS detection, version detection, script scanning, and traceroute |
| `nmap -sU <target>` | Scan UDP ports |
| `nmap -Pn <target>` | Skip host discovery (treat host as online) |
| `nmap -T0` … `nmap -T5` | Timing templates from paranoid (0) to insane (5) |
| `nmap -oA <basename> <target>` | Output results in all formats (normal, XML, grepable) |

## Important Options

| Option | Description |
|---|---|
| `-A` | Aggressive scan (OS detection, version detection, script scanning, traceroute) |
| `-p-` | Scan every TCP port (1–65535) |
| `-Pn` | Skip host discovery / assume host is up |
| `-T3` | "Normal" timing template (default) |
| `--script=<name>` | Run a specific NSE script |
| `-sS` | TCP SYN ("stealth") scan (default with root privileges) |
| `-sT` | TCP connect scan (used without root privileges) |

## Examples

Full port, service/version, OS, and script scan used during recon:

```bash
sudo nmap -A -p- -T3 10.113.170.172
```

Faster targeted scan of common web/SSH/FTP ports:

```bash
nmap -sV -p 21,22,80 <target>
```

## Common Mistakes

- Forgetting `sudo`, which disables SYN scanning and OS fingerprinting.
- Using default port ranges and missing services running on non-standard ports — always confirm with `-p-` when time allows.
- Using `-A` on a large network sweep — it is slow and noisy; better suited to single-host deep scans.

## References

- [Nmap Reference Guide](https://nmap.org/book/man.html)

## Key Takeaways

- `-A -p- -T3` is a solid "deep scan" combination for a single host when time is not critical.
- Always cross-reference detected service versions against known CVEs before moving to exploitation.
