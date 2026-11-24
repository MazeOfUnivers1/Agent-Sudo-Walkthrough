# Hashcat Cheat Sheet

> [!NOTE]
> John the Ripper was the hash-cracking tool actually used in this engagement (see [05-Credentials](../docs/05-Credentials/README.md)). This sheet is provided as general reference material for Hashcat, a commonly paired/alternative cracking tool, since it was listed among the requested cheat sheets.

## Overview

Hashcat is a GPU-accelerated password recovery tool supporting a very wide range of hash types, commonly used as a faster alternative to CPU-based crackers like John the Ripper for large wordlists or complex rule-based attacks.

## Common Syntax

```bash
hashcat -m <hash-mode> -a <attack-mode> <hashfile> <wordlist>
```

## Useful Commands

| Command | Description |
|---|---|
| `hashcat -m 0 hash.txt rockyou.txt` | Crack MD5 hashes using a wordlist |
| `hashcat -m 1000 hash.txt rockyou.txt` | Crack NTLM hashes |
| `hashcat -m 17200 hash.txt rockyou.txt` | Crack PKZIP (classic ZIP) hashes |
| `hashcat -m 13600 hash.txt rockyou.txt` | Crack WinZip (AES) archive hashes |
| `hashcat --show hash.txt` | Show previously cracked results |
| `hashcat -a 3 hash.txt ?a?a?a?a?a?a` | Brute-force mask attack (6-character, all charsets) |

## Important Options

| Option | Description |
|---|---|
| `-m` | Hash mode/type (e.g., `0` = MD5, `1000` = NTLM) |
| `-a` | Attack mode (`0` = wordlist, `1` = combination, `3` = mask/brute-force) |
| `-r` | Path to a rule file for wordlist mangling |
| `-o` | Output file for cracked results |
| `--force` | Force execution despite warnings (use with caution) |

## Examples

Cracking a WinZip/AES-encrypted archive hash (the format produced by `zip2john` for modern ZIPs, as encountered in [05-Credentials](../docs/05-Credentials/README.md)):

```bash
hashcat -m 13600 hash.txt /usr/share/wordlists/rockyou.txt
```

## Common Mistakes

- Selecting the wrong `-m` hash mode — always verify the hash format before cracking (`hashid`, `hash-identifier`, or the tool that generated the hash).
- Forgetting `--show` after a session, resulting in missed cracked passwords when scrollback is lost.
- Running large mask attacks (`-a 3`) without estimating runtime first — extremely large keyspaces can take impractically long.

## References

- [Hashcat Official Documentation](https://hashcat.net/wiki/)
- [Hashcat Example Hashes (mode reference)](https://hashcat.net/wiki/doku.php?id=example_hashes)

## Key Takeaways

- Hashcat and John the Ripper serve similar purposes; Hashcat's GPU acceleration typically outperforms John's CPU-based cracking for large wordlists or rule-based mangling.
- Correctly identifying the hash mode (`-m`) is the single most important step before attempting to crack any hash.
