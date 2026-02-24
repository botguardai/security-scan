# BotGuard Security Scan - GitHub Action

Automated security scanning for AI agents in your CI/CD pipeline. Tests for prompt injection, jailbreaks, data extraction, and 70+ attack scenarios aligned to OWASP LLM Top 10.

## Quick Start

```yaml
name: AI Security Scan
on: [pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: botguardai/security-scan@v1
        with:
          api-key: ${{ secrets.BOTGUARD_API_KEY }}
          endpoint: https://my-agent.com/chat
          description: "Customer support chatbot"
          fail-threshold: 80
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `api-key` | Yes | — | BotGuard API key |
| `endpoint` | No | — | Agent API endpoint URL |
| `description` | No | `AI agent` | Agent description |
| `system-prompt` | No | — | System prompt (text or file path) |
| `fail-threshold` | No | `0` | Minimum score to pass (0 = never fail) |
| `categories` | No | — | Comma-separated attack categories |
| `attack-count` | No | — | Number of attacks to run |
| `format` | No | `table` | Output format: table, json, summary |
| `comment` | No | `true` | Post results as PR comment |
| `api-url` | No | — | Custom API URL (self-hosted) |

## Outputs

| Output | Description |
|---|---|
| `score` | Security score (0-100) |
| `passed` | Whether threshold was met (`true`/`false`) |
| `report-url` | Link to full security report |
| `session-id` | Scan session ID |

## Examples

### Block PRs Below Security Threshold

```yaml
- uses: botguardai/security-scan@v1
  with:
    api-key: ${{ secrets.BOTGUARD_API_KEY }}
    system-prompt: ./prompts/system.md
    fail-threshold: 80
```

### Scan Specific Attack Categories

```yaml
- uses: botguardai/security-scan@v1
  with:
    api-key: ${{ secrets.BOTGUARD_API_KEY }}
    endpoint: https://my-bot.com/chat
    categories: "jailbreak,prompt_injection,data_extraction"
    attack-count: 50
```

### Use Score in Downstream Steps

```yaml
- uses: botguardai/security-scan@v1
  id: scan
  with:
    api-key: ${{ secrets.BOTGUARD_API_KEY }}
    endpoint: https://my-bot.com/chat

- run: echo "Score is ${{ steps.scan.outputs.score }}"
```

### Scan on Schedule

```yaml
name: Weekly Security Scan
on:
  schedule:
    - cron: '0 9 * * 1'

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: botguardai/security-scan@v1
        with:
          api-key: ${{ secrets.BOTGUARD_API_KEY }}
          endpoint: https://my-bot.com/chat
          fail-threshold: 70
```

## PR Comment

When `comment: true` (default), the action posts a summary on pull requests:

| Metric | Value |
|---|---|
| **Score** | **85/100** |
| Passed | 42 |
| Failed | 8 |
| Violations | 12 |

## Getting Started

1. Sign up at [botguard.dev](https://botguard.dev)
2. Go to **Account > API Keys** and create a CI/CD key
3. Add the key as a GitHub secret: `BOTGUARD_API_KEY`
4. Add the action to your workflow

## License

MIT
