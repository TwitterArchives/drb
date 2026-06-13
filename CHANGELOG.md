# Changelog

All notable changes to robofinder are documented here.  
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [Unreleased]

---

## [0.2.2] – 2026-06-13

### Fixed
- Fixed output header showing in silent mode

---

## [0.2.1] – 2026-06-13

### Fixed
- Just fixed a UI issue about showing the banner and `--silent` switch

---

## [0.2.0] – 2026-06-13

### Added
- Token-bucket rate limiter (`-r` / `--rate-limit`, default 2 req/s)
- `--cooldown` flag to wait between domains (default 10s)
- `-f json` / `-f both` output format support
- `-o` flag: no value = terminal, with value = save to file
- Exponential backoff with jitter on all retries (up to 7 attempts)
- Retry on SSL, connection, timeout, and 429 errors for CDX requests
- Debug logs go to stderr (never pollutes stdout for piping)
- Banner goes to stderr (safe to pipe to jq)

### Changed
- Removed `-l` flag (use `-u` for both URLs and files)
- Sequential requests by default (`--threads` default changed from 10 to 1)
- Removed unnecessary 10s sleep at end of run
- Improved help text with grouped options (target, output, config)
- Updated banner with version display

### Fixed
- Double-slash URL bug (`https://example.com//robots.txt`)
- CDX requests had no retry logic (SSL/timeout errors silently dropped data)
- `ReadTimeout` not caught by exception handler
- Banner polluting stdout when piping to jq or other tools

---

## [0.1.0] – 2026-06-08

### Added
- Initial release of robofinder
- PyPI packaging: `pip install robofinder` now works
- CHANGELOG.md added for tracking future changes