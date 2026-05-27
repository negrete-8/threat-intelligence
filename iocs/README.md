# IOCs

Structured indicators of compromise extracted from captured malware samples.

## Files

| File | Family | Date | Format |
|------|--------|------|--------|
| [outlaw-2026-05.csv](./outlaw-2026-05.csv) | Outlaw / Shellbot / PerlBot | 2026-05-17 | CSV |

## CSV Schema

```
type,value,context,family,first_seen,reference
```

- **type** — IOC type: `sha256`, `ip:port`, `subnet`, `wallet`, `filename`, `process`, `ssh-key-tag`, `irc-nick`
- **value** — the indicator value
- **context** — human-readable description
- **family** — malware family
- **first_seen** — date of first observation
- **reference** — link to public sample/IOC page (when available)

## Usage

These CSVs are machine-readable for SIEM/EDR ingestion. Import directly into MISP, Splunk, or any tool that accepts CSV indicators.
