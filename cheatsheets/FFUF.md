# FFUF Cheat Sheet

> [!NOTE]
> FFUF was present in the lab toolkit (`FFUF/` directory, `resultofFFUF.txt`) but no FFUF command executions were captured in the available screenshots/notes for this engagement. This sheet is provided as general reference material for the tool.

## Overview

FFUF ("Fuzz Faster U Fool") is a fast web fuzzer used for directory/file brute-forcing, virtual host discovery, and parameter fuzzing.

## Common Syntax

```bash
ffuf -w <wordlist> -u <url-with-FUZZ-keyword>
```

## Useful Commands

| Command | Description |
|---|---|
| `ffuf -w wordlist.txt -u http://target/FUZZ` | Directory/file brute-forcing |
| `ffuf -w wordlist.txt -u http://FUZZ.target.com` | Virtual host / subdomain fuzzing |
| `ffuf -w wordlist.txt -u http://target/?param=FUZZ` | Parameter value fuzzing |
| `ffuf -w wordlist.txt -u http://target/FUZZ -e .php,.txt,.html` | Fuzz with file extensions appended |

## Important Options

| Option | Description |
|---|---|
| `-w` | Path to the wordlist |
| `-u` | Target URL; `FUZZ` marks the injection point |
| `-e` | Comma-separated list of extensions to append to each word |
| `-fc` | Filter out responses by status code |
| `-fs` | Filter out responses by response size |
| `-mc` | Match only responses with specific status codes |
| `-t` | Number of concurrent threads |
| `-H` | Add a custom header (e.g., a custom `User-Agent`) |

## Examples

```bash
ffuf -w /usr/share/wordlists/dirb/common.txt -u http://target/FUZZ -e .php,.html
```

Fuzzing with a custom header (relevant when a target implements header-based logic, as seen in [03-Web](../docs/03-Web/README.md)):

```bash
ffuf -w wordlist.txt -u http://target/ -H "User-Agent: FUZZ"
```

## Common Mistakes

- Forgetting the `FUZZ` keyword in the URL, causing FFUF to fail or fuzz nothing.
- Not filtering out noisy default responses (`-fc`/`-fs`), leading to a flood of false positives.
- Using too many threads (`-t`) against a fragile target, causing instability or false negatives.

## References

- [FFUF GitHub Repository](https://github.com/ffuf/ffuf)

## Key Takeaways

- FFUF's `FUZZ` keyword can be placed in the URL path, subdomain, headers, or POST body, making it versatile for many enumeration scenarios beyond simple directory brute-forcing.
