# Outlaw / Shellbot / PerlBot — Honeypot Capture & Analysis

> **Date captured:** 2026-05-17
> **Source:** Cowrie SSH honeypot (VPS, Madrid)
> **Analyst:** [NullBlue](https://www.virustotal.com/gui/user/NullBlue)
> **Disclosure:** 4 samples to MalwareBazaar, 5 IOCs to ThreatFox, 4 samples to VirusTotal

## Executive Summary

A full Outlaw botnet kit (`dota3.tar.gz`) was dropped on an SSH honeypot via brute-force authentication. The TAR archive contained 26 files including three UPX-packed ELF binaries (one XMRig miner, two SSH brute-forcers) and a Perl IRC bot ("Stealth ShellBot by B0tchZ") with three C2 variants.

**Three of the four submitted samples were not present in any public threat intelligence database at the time of capture** (VirusTotal, MalwareBazaar, ThreatFox, Hybrid Analysis). The analyst became the first submitter worldwide for these samples.

## Attribution Indicators

| Indicator | Value |
|-----------|-------|
| Family | Outlaw (a.k.a. Shellbot, PerlBot, Dota) |
| Origin | Romanian (confirmed by code comments: `adresanoua1/2`) |
| Operator activity | Latest compile date 2026-05-12 (5 days before capture) |
| Algo / mining | cryptonight-monerov7/v8 |
| C2 protocol | IRC over port 443 |

## Attack Chain

```
1. SSH brute-force (root/admin credentials)
       │
2. Drop dota3.tar.gz in /tmp
       │
3. Extract .rsync/{a,b,c}/ structure
       │
4. Run init scripts:
       ├── Kill competing miners and botnets
       ├── Install SSH key in ~/.ssh/authorized_keys (mdrfckr signature)
       ├── chattr +ia (make key immutable)
       │
5. Launch components:
       ├── a/kswapd00     → XMRig miner (CPU mining)
       ├── b/run          → Perl IRC bot (C2 communication + DDoS)
       └── c/kthreadadd*  → SSH brute-forcer (lateral propagation)
```

## Kit Structure

```
dota3.tar.gz
└── .rsync/
    ├── init, init2, initall          # Entry-point shell scripts
    ├── a/
    │   ├── kswapd00                  # XMRig miner (2.2 MB UPX-packed)
    │   ├── init01                    # Competition killer
    │   ├── a, run, stop              # Lifecycle scripts
    │   └── delsshd, rmkwork          # Cleanup
    ├── b/
    │   └── run                       # Main propagator + SSH key installer
    └── c/
        ├── kthreadadd64              # SSH brute-forcer 64-bit (1.1 MB)
        ├── kthreadadd32              # SSH brute-forcer 32-bit (1.2 MB)
        └── go                        # Brute-forcer launcher
```

## Sample Hashes

### Original (UPX-packed)

| File | SHA-256 | MalwareBazaar | VirusTotal |
|------|---------|---------------|------------|
| dota3.tar.gz | `e3177a205114b0645b340d5537feec0137b6d71cece0430f1c2453e826eb1777` | [link](https://bazaar.abuse.ch/sample/e3177a205114b0645b340d5537feec0137b6d71cece0430f1c2453e826eb1777/) | [link](https://www.virustotal.com/gui/file/e3177a205114b0645b340d5537feec0137b6d71cece0430f1c2453e826eb1777) |
| kthreadadd64 | `94d45a4da52d0b4593387673b8579892c866b19d71408dde550800050a3d65b8` | [link](https://bazaar.abuse.ch/sample/94d45a4da52d0b4593387673b8579892c866b19d71408dde550800050a3d65b8/) | [link](https://www.virustotal.com/gui/file/94d45a4da52d0b4593387673b8579892c866b19d71408dde550800050a3d65b8) |
| kthreadadd32 | `289abf2d87fb23983de053535a7e2946a90865c2d96f80f94f491e28c388fb1f` | [link](https://bazaar.abuse.ch/sample/289abf2d87fb23983de053535a7e2946a90865c2d96f80f94f491e28c388fb1f/) | [link](https://www.virustotal.com/gui/file/289abf2d87fb23983de053535a7e2946a90865c2d96f80f94f491e28c388fb1f) |
| kswapd00 | `a01e58ce02a7b7fe9e5368878d30ce576d958069b30e952ff68686e2264b3292` | [link](https://bazaar.abuse.ch/sample/a01e58ce02a7b7fe9e5368878d30ce576d958069b30e952ff68686e2264b3292/) | [link](https://www.virustotal.com/gui/file/a01e58ce02a7b7fe9e5368878d30ce576d958069b30e952ff68686e2264b3292) |

### Unpacked (UPX decompressed)

| File | SHA-256 |
|------|---------|
| kswapd00 unpacked | `c9d9583fb44131fe57df0c99c74e460973dd9c1f38ac57a7c3c8238be1e350eb` |
| kthreadadd64 unpacked | `deac427e9b81607843c1e145103be8fd16960e533d2a8c0c003d7e094047923b` |
| kthreadadd32 unpacked | `192604729aada8ede20a961fb125e2acb62f692413177754baf7068a3e654412` |

## Network IOCs

### IRC Command & Control

| IP:Port | Channel | Role |
|---------|---------|------|
| `179.43.139.83:443` | #001 | IRC C2 (also obfuscated as `0xB32B8B53`) |
| `209.99.186.7:443` | #009 | IRC C2 |

**Bot admins (IRC nicks):** `molly`, `polly`

### Mining Pools

| IP:Port | Notes |
|---------|-------|
| `179.43.139.83:80` | Same IP as IRC C2 #001 (multi-role) |
| `179.43.139.85:442` | Pool secondary |
| `179.43.180.84:80` | Obfuscated as `0xB32BB454` |

### Infrastructure Cluster

- **Subnet:** `179.43.139.0/29` — dedicated to Outlaw operations
- **Same `/29` references in code (commented out):** `179.43.139.80–85`

## Host IOCs

### SSH Persistence Key

The infamous `mdrfckr` SSH key — the most well-known Outlaw IOC, documented by Akamai and TrendMicro since 2018.

```
ssh-rsa AAAAB3NzaC1...mdrfckr
```

Installed in `~/.ssh/authorized_keys` with `chattr +ia` to make the file immutable (resistant to deletion without root).

### Filesystem Artifacts

- `~/.rsync/` — main payload directory
- `~/.configrc7/` — persistence marker checked by `initall`
- `~/.nullcach*` — cleanup target
- `~/.firefoxcat` — cleanup target

### Process Names (kernel-thread impersonation)

- `kswapd00` — impersonates kernel swap daemon
- `kthreadadd` — impersonates kernel thread

## Payload Analysis: Perl IRC Bot

The TAR contains a Perl IRC bot identified as "Stealth ShellBot v0.2a" by `B0tchZ`. Three variants embedded, each pointing to a different C2:

| Variant | Encoded C2 | Decoded |
|---------|-----------|---------|
| 1 | `0xB32B8B53` (hex) | `179.43.139.83` |
| 2 | `179.43.139.83` | (literal) |
| 3 | `209.99.186.7` | (literal) |

**Capabilities:**
- DDoS: UDP flood (`udp`, `udpfaixa`), TCP, ICMP/IGMP multi-protocol (`oldpack`)
- Port scanning: targets 21, 22, 23, 25, 53, 80, 110, 143, 6665
- Reverse shell: `conback host port` (`bash -i` over socket)
- Arbitrary download: `download URL filename`
- Remote Perl `eval` (full RCE via IRC channel)
- DCC SEND/GET file transfer

## XMRig Miner Configuration

Extracted from the UPX-unpacked `kswapd00` binary:

```json
{
  "algo": "cryptonight-monerov7/v8",
  "user": "483fmPjXwX75xmkaJ3dm4vVGWZLHn3GDuKycHypVLr9SgiT6oaZgVh26iZRpwKEkTZCAmUS8tykuwUorM3zGtWxPBFqwuxS",
  "url": [
    "179.43.139.83:80",
    "179.43.139.85:442",
    "179.43.180.84:80"
  ]
}
```

## SSH Brute-Forcer (kthreadadd)

The `c/kthreadadd64` / `kthreadadd32` binaries are variants of **haiduc** — a Romanian SSH brute-forcer classically associated with Outlaw operations. Launched by `c/go` with:

```bash
./kthreadadd -t 525 -f 1 -s 14 -S 8 -p 0 -d 1 passwords.txt ip-list.txt
```

525 concurrent threads. Used for lateral propagation across the internet.

## Detection Impact

The three novel samples were submitted to VirusTotal with the analyst as first submitter. Within hours, detection rates rose from `0/72` to `40/62` as AV vendors processed the samples. This propagation directly improves defense for any organization using VirusTotal-integrated security products.

## Threat Intelligence Submissions

### MalwareBazaar
- 4 samples uploaded with tags: `Outlaw`, `PerlBot`, `Shellbot`, `UPX`, `honeypot`
- All four indexed and publicly accessible

### ThreatFox (5 IOCs, +25 reward points)
| IOC | Type | Threat |
|-----|------|--------|
| `179.43.139.83:443` | ip:port | botnet_cc |
| `209.99.186.7:443` | ip:port | botnet_cc |
| `179.43.139.83:80` | ip:port | payload (mining pool) |
| `179.43.139.85:442` | ip:port | payload (mining pool) |
| `179.43.180.84:80` | ip:port | payload (mining pool) |

## References

- [Akamai — Outlaw Botnet Analysis (2018)](https://www.akamai.com/blog/security/an-old-friend-coming-back)
- [TrendMicro — Outlaw Hacking Group Update](https://www.trendmicro.com)
- [AT&T Alien Labs — mdrfckr SSH key tracking](https://cybersecurity.att.com)
