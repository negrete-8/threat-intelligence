# threat-intelligence

![Threat Intel](https://img.shields.io/badge/Threat-Intelligence-red)
![Submitter](https://img.shields.io/badge/MalwareBazaar-nullblue67-blue)
![VT](https://img.shields.io/badge/VirusTotal-NullBlue-394EFF?logo=virustotal&logoColor=white)
![ThreatFox](https://img.shields.io/badge/ThreatFox-Contributor-orange)

Malware samples and IOCs captured from a multi-service honeypot infrastructure and reported to public threat intelligence platforms.

## Public Profiles

| Platform | Profile | Type |
|----------|---------|------|
| MalwareBazaar | [nullblue67](https://bazaar.abuse.ch/user/38492/) | Malware samples |
| ThreatFox | [97135](https://threatfox.abuse.ch/user/97135/) | Indicators of compromise |
| VirusTotal | [NullBlue](https://www.virustotal.com/gui/user/NullBlue) | Sample analysis |

## Contribution Stats

| Metric | Count |
|--------|-------|
| Malware samples submitted | 4 |
| Novel samples (first submitter worldwide) | 3 |
| IOCs reported | 5 |
| Botnet C2 infrastructure mapped | 1 cluster (/29 subnet) |
| Monero wallets identified | 1 operator |

## Reports

| Date | Family | Report |
|------|--------|--------|
| 2026-05-17 | Outlaw / Shellbot / PerlBot | [Full analysis](./reports/outlaw-shellbot-2026-05.md) |

## Repository Structure

```
threat-intelligence/
├── reports/            Technical malware analysis writeups
├── iocs/               Structured indicators (CSV/JSON)
└── samples/            Sample hash references with MB/VT links
```

## Methodology

1. **Capture** — multi-service honeypot (SSH, Web, Redis, Docker, PostgreSQL) on internet-exposed VPS
2. **Analyze** — extract IOCs, unpack binaries (UPX), identify C2 infrastructure
3. **Verify** — cross-reference with VirusTotal, Hybrid Analysis, MalwareBazaar
4. **Report** — submit samples to MalwareBazaar, IOCs to ThreatFox
5. **Document** — publish technical writeup with full attack chain

## Related Repositories

- [honeypot](https://github.com/negrete-8/honeypot) — Multi-service honeypot infrastructure that captures the samples documented here

## Legal Notice

> All samples were captured in a controlled honeypot environment specifically designed to attract and analyze malicious activity.
> IOCs and analysis are published for defensive purposes and shared with the threat intelligence community.
