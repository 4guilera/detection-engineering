# detection-engineering

Sigma detection rules for my purple team homelab, validated on every commit with GitHub Actions.

## What is this?

A collection of detection rules written in [Sigma](https://sigmahq.io/) — a vendor-agnostic format that compiles down to whatever your SIEM speaks (Splunk SPL, Elastic KQL, Microsoft Sentinel, etc.). Each rule maps to a MITRE ATT&CK technique and is tested against my homelab's Splunk instance.

The CI pipeline validates syntax and converts every rule to Splunk SPL on push. If a rule is malformed or won't compile, the pipeline fails before it can reach the SIEM.

## Rules

| Rule | ATT&CK | Level |
|---|---|---|
| Brute Force - Multiple Failed Logons | T1110.001 | High |
| Suspicious Encoded PowerShell | T1059.001 | High |
| Office Application Spawning Interpreter | T1566.001 / T1204.002 | High |
| LSASS Memory Access (Credential Dumping) | T1003.001 | Critical |
| New Service Installed | T1543.003 | Medium |
| Local User Account Created | T1136.001 | Medium |

## Repo layout

```
├── .github/workflows/
│   └── validate.yml          # CI — lint + Splunk conversion
├── rules/
│   └── windows/
│       ├── authentication/   # Logon events
│       ├── process_creation/ # Sysmon process + access events
│       └── persistence/      # Services, accounts
└── README.md
```

## CI Pipeline

Every push to `main` (or PR against it) triggers:

1. **Syntax validation** — `sigma check` ensures rules conform to the Sigma schema
2. **Splunk conversion** — `sigma convert -t splunk` compiles rules to SPL, catching field mapping issues

[![Validate Sigma Rules](https://github.com/4guilera/detection-engineering/actions/workflows/validate.yml/badge.svg)](https://github.com/4guilera/detection-engineering/actions/workflows/validate.yml)

## Writing a new rule

Rules follow the [Sigma specification](https://sigmahq.io/docs/basics/rules.html). Drop a `.yml` file in the appropriate `rules/` subdirectory and push. The pipeline handles the rest.

## Related

- [range-as-code](https://github.com/4guilera/range-as-code) — the lab environment these detections run against
- [homelab](https://github.com/4guilera/homelab) — architecture overview
