<h1 align="center">Robofinder</h1>

<p align="center">
  <img src="https://img.shields.io/pypi/v/robofinder?color=blue" alt="PyPI">
  <img src="https://img.shields.io/pypi/dm/robofinder" alt="Downloads">
  <img src="https://img.shields.io/badge/python-3.8%2B-blue" alt="Python 3.8+">
  <img src="https://img.shields.io/github/license/Spix0r/robofinder" alt="License">
  <img src="https://img.shields.io/github/stars/Spix0r/robofinder?style=social" alt="Stars">
</p>

<pre align="center">
 _____       _            __ _           _           
|  __ \     | |          / _(_)         | |          
| |__) |___ | |__   ___ | |_ _ _ __   __| | ___ _ __ 
|  _  // _ \| '_ \ / _ \|  _| | '_ \ / _` |/ _ \ '__|
| | \ \ (_) | |_) | (_) | | | | | | | (_| |  __/ |   
|_|  \_\___/|_.__/ \___/|_| |_|_| |_|\__,_|\___|_|   
                                                     
</pre>

<p align="center">
  Uncover hidden endpoints by mining every historical <code>robots.txt</code> snapshot from the Wayback Machine.
</p>

---

## Why Robofinder?

Sites regularly scrub sensitive paths from `robots.txt` — but the **Wayback Machine keeps every version ever crawled**.

Robofinder queries Archive.org's CDX API to pull *all* historical `robots.txt` snapshots for a target, deduplicates every `Allow`, `Disallow`, and `Sitemap` directive, and prints the full list. Paths that were quietly removed from production may still be alive and reachable.

**Built for:** bug bounty recon · OSINT · attack-surface mapping · forgotten endpoint discovery

---

## Install

```bash
pip install robofinder
```

<details>
<summary>Install from source</summary>

```bash
git clone https://github.com/Spix0r/robofinder
cd robofinder
pip install .
```
</details>

---

## Quick start

```bash
# Single target
robofinder -u https://example.com

# Full URLs ready to probe
robofinder -u https://example.com -c

# Pipe straight into httpx or nuclei
robofinder -u domains.txt -c | httpx -silent -mc 200
robofinder -u https://example.com -c | nuclei -t exposures/
```

---

## Usage

```
robofinder -u URL [options]
```

| Flag | Long form | Default | Description |
|------|-----------|---------|-------------|
| `-u` | `--url` | — | Target URL or file with one URL per line |
| `-o` | `--output` | — | Output to terminal (no value) or save to file (with value) |
| `-f` | `--format` | `txt` | Output format: `txt`, `json`, or `both` |
| `-c` | | — | Prefix each path with the target URL (full URLs) |
| `-p` | | — | Extract URL parameters from historical paths |
| `-t` | `--threads` | `1` | Number of fetch threads |
| `-r` | `--rate-limit` | `2.0` | Max requests/sec sent to Archive.org |
| | `--cooldown` | `10` | Seconds to wait between domains |
| `-s` | `--silent` | — | Suppress the banner |
| | `--debug` | — | Verbose debug output (goes to stderr) |

### Scan a list of domains

Create `domains.txt`:
```
https://example.com
https://target.org
api.example.com
```

```bash
robofinder -u domains.txt -o all_paths.txt
```

### JSON output with jq

```bash
# Save to file
robofinder -u https://example.com -f json -o results

# Pipe to jq
robofinder -u https://example.com -f json | jq '.["example.com"].paths[]'

# Count paths per domain
robofinder -u domains.txt -f json | jq 'to_entries[] | "\(.key): \(.value.count) paths"'
```

### Extract forgotten URL parameters

```bash
robofinder -u https://example.com -p
# returns parameter names found in historical paths, e.g.:
# id
# token
# redirect_url
```

---

## Rate limiting

Robofinder ships with a **token-bucket rate limiter** so it never hammers Archive.org.

- Default: **2 req/s** — well within Archive.org's tolerance.
- On HTTP **429**: exponential back-off with automatic retry.
- On timeout/connection errors: retries with backoff (up to 7 attempts).
- Override with `-r`: use `-r 0.5` to be conservative or `-r 5` on a fast connection.

```bash
# Conservative mode
robofinder -u https://example.com -r 1 --cooldown 30
```

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

---

## License

[MIT](LICENSE) © [Spix0r](https://github.com/Spix0r)
