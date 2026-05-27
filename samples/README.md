# Samples

Reference table of malware samples captured and submitted to public threat intelligence platforms. Files themselves are **not** stored in this repository — they are available through MalwareBazaar.

## Samples Submitted

### Outlaw / Shellbot / PerlBot (2026-05-17)

| SHA-256 | Component | Size | Type | Novel | Tags |
|---------|-----------|------|------|-------|------|
| `e3177a20...82` | dota3.tar.gz (full kit) | 4.3 MB | gz | ✅ | Outlaw, PerlBot, Shellbot |
| `94d45a4d...b8` | kthreadadd64 (SSH bruteforcer) | 1.1 MB | ELF | ✅ | Outlaw, UPX, haiduc |
| `289abf2d...1f` | kthreadadd32 (SSH bruteforcer) | 1.2 MB | ELF | ✅ | Outlaw, UPX, haiduc |
| `a01e58ce...92` | kswapd00 (XMRig miner) | 2.2 MB | ELF | ❌ | Outlaw, XMRig, UPX |

**Novel** = sample was not present in any public threat intel database at time of submission (analyst is first submitter worldwide).

## Full Sample Links

See [reports/outlaw-shellbot-2026-05.md](../reports/outlaw-shellbot-2026-05.md) for the complete hash table with direct MalwareBazaar and VirusTotal links.

## Sample Acquisition

Samples are publicly downloadable from MalwareBazaar for verified researchers:

```bash
curl -X POST https://mb-api.abuse.ch/api/v1/ \
  -H "Auth-Key: YOUR_MB_API_KEY" \
  -F "query=get_file" \
  -F "sha256_hash=<HASH>" \
  -o sample.zip
# Password to unzip: infected
```

> Always handle samples in an isolated analysis environment. Never execute on production systems.
