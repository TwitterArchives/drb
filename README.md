<h1 align="center">RoboFinder</h1>

<p align="center">
  Uncover hidden endpoints by mining every historical <code>robots.txt</code> snapshot from the Wayback Machine.
</p>

<p align="center">
  <a href="https://pypi.org/project/robofinder/"><img src="https://img.shields.io/pypi/v/robofinder?color=blue" alt="PyPI"></a>
  <a href="https://pypi.org/project/robofinder/"><img src="https://img.shields.io/pypi/dm/robofinder" alt="Downloads"></a>
  <img src="https://img.shields.io/badge/python-3.8%2B-blue" alt="Python 3.8+">
  <a href="LICENSE"><img src="https://img.shields.io/github/license/Spix0r/robofinder" alt="License"></a>
  <a href="https://github.com/Spix0r/robofinder/stargazers"><img src="https://img.shields.io/github/stars/Spix0r/robofinder?style=social" alt="Stars"></a>
</p>

---

## Why RoboFinder?

Sites regularly scrub sensitive paths from `robots.txt` — but the **Wayback Machine keeps every version ever crawled**.

RoboFinder queries Archive.org's CDX API to pull *all* historical `robots.txt` snapshots for a target, deduplicates every `Allow`, `Disallow`, and `Sitemap` directive, and prints the full list. Paths that were quietly removed from production may still be alive and reachable.

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
robofinder -l domains.txt -c | httpx -silent -mc 200
robofinder -u https://example.com -c | nuclei -t exposures/
```

---

## Usage

```
robofinder [-u URL | -l FILE] [options]
```

| Flag | Long form | Default | Description |
|------|-----------|---------|-------------|
| `-u` | `--url` | — | Single target URL |
| `-o` | `--output` | — | Save results to a file |
| `-t` | `--threads` | `10` | Number of fetch threads |
| `-r` | `--rate-limit` | `2.0` | Max requests/sec sent to Archive.org |
| `-c` | | — | Prefix each path with the target URL |
| `-p` | | — | Extract URL parameters from historical paths |
| `-s` | `--silent` | — | Suppress the banner |
| | `--debug` | — | Verbose debug output |

### Scan a list of domains

Create `domains.txt`:
```
https://example.com
https://target.org
api.example.com
```

```bash
robofinder -l domains.txt -o all_paths.txt
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

RoboFinder ships with a **token-bucket rate limiter** shared across all threads so it never hammers Archive.org.

- Default: **2 req/s** — well within Archive.org's tolerance.
- On HTTP **429**: exponential back-off (1 s → 2 s → 4 s … max 30 s) with automatic retry.
- Override with `-r`: use `-r 0.5` to be conservative or `-r 5` on a fast connection.

```bash
robofinder -u https://example.com -r 1
```

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

---

## License

[MIT](LICENSE) © [Spix0r](https://github.com/Spix0r)
