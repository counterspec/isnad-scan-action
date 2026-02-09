# 🔍 ISNAD Scan Action

Security scanner for AI agent skills. Detect dangerous patterns before they reach production.

[![ISNAD Protocol](https://img.shields.io/badge/powered%20by-ISNAD-green)](https://isnad.md)

## Features

- 🛡️ **69+ detection patterns** — shell injection, credential theft, filesystem access, network exfiltration
- ⚡ **Fast** — scans in seconds, not minutes  
- 🔧 **Configurable** — fail on WARN, DANGER, or never
- 📊 **Rich output** — detailed findings with line numbers

## Quick Start

```yaml
name: Security Scan

on: [push, pull_request]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: isnad-protocol/isnad-scan-action@v1
        with:
          path: './skills'
          fail-on: 'DANGER'
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `path` | Path to scan | `.` |
| `fail-on` | Fail on trust level (`WARN`, `DANGER`, `never`) | `DANGER` |
| `verbose` | Show verbose output | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `trust-level` | Result: `SAFE`, `WARN`, or `DANGER` |
| `findings-count` | Number of security findings |
| `report` | Full scan report |

## Example: Comment on PR

```yaml
- uses: isnad-protocol/isnad-scan-action@v1
  id: scan
  with:
    path: './skills'
    fail-on: 'never'  # Don't fail, just report

- name: Comment on PR
  if: github.event_name == 'pull_request'
  uses: actions/github-script@v7
  with:
    script: |
      const trustLevel = '${{ steps.scan.outputs.trust-level }}';
      const emoji = trustLevel === 'SAFE' ? '✅' : trustLevel === 'WARN' ? '⚠️' : '🚨';
      
      github.rest.issues.createComment({
        owner: context.repo.owner,
        repo: context.repo.repo,
        issue_number: context.issue.number,
        body: `## ${emoji} ISNAD Security Scan\n\n**Trust Level:** ${trustLevel}\n**Findings:** ${{ steps.scan.outputs.findings-count }}\n\n\`\`\`\n${{ steps.scan.outputs.report }}\n\`\`\``
      });
```

## Trust Levels

| Level | Description |
|-------|-------------|
| ✅ **SAFE** | No concerning patterns detected |
| ⚠️ **WARN** | Patterns that need review (env access, file writes) |
| 🚨 **DANGER** | High-risk patterns (shell exec, credential access, network calls) |

## Links

- [ISNAD Protocol](https://isnad.md)
- [isnad-scan on PyPI](https://pypi.org/project/isnad-scan/)
- [Report Issues](https://github.com/isnad-protocol/isnad-scan-action/issues)

---

Built by [ISNAD Protocol](https://isnad.md) — Trust infrastructure for AI agents.
