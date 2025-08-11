# PromptProof GitHub Action

[![CI](https://img.shields.io/github/actions/workflow/status/geminimir/promptproof-action/ci.yml?branch=main)](https://github.com/geminimir/promptproof-action/actions)
[![Marketplace](https://img.shields.io/badge/Marketplace-PromptProof%20Eval-blue?logo=github)](https://github.com/marketplace/actions/promptproof-eval)
[![Node](https://img.shields.io/badge/node-%3E%3D18-brightgreen)](https://nodejs.org)

Deterministic LLM testing in your CI/CD pipeline. This action evaluates recorded LLM outputs against defined contracts and fails PRs when violations are detected.

## Features

- 🔒 **Zero network calls** - Tests run on recorded fixtures
- 📊 **Rich reporting** - HTML, JUnit, JSON output formats
- 💬 **PR comments** - Automatic violation summaries
- 📈 **Budget tracking** - Cost and latency monitoring
- 🎯 **Flexible checks** - JSON schema, regex, custom functions

## Quick Start

```yaml
name: PromptProof
on: [pull_request]

jobs:
  eval:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: geminimir/promptproof-action@v0
        with:
          config: promptproof.yaml
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `config` | Path to promptproof.yaml | `promptproof.yaml` |
| `format` | Output format (console/html/junit/json) | `html` |
| `mode` | Override mode (fail/warn) | Use config value |
| `node-version` | Node.js version | `20` |

## Outputs

| Output | Description |
|--------|-------------|
| `violations` | Number of violations found |
| `passed` | Number of fixtures that passed |
| `failed` | Number of fixtures that failed |
| `report-path` | Path to generated report |

## Configuration

Create a `promptproof.yaml` file in your repository:

```yaml
schema_version: pp.v1
fixtures: fixtures/outputs.jsonl
checks:
  - id: no_pii
    type: regex_forbidden
    target: text
    patterns:
      - "[A-Z0-9._%+-]+@[A-Z0-9.-]+\\.[A-Z]{2,}"
budgets:
  cost_usd_per_run_max: 0.50
  latency_ms_p95_max: 2000
mode: fail
```

## Examples

### Basic Usage

```yaml
- uses: geminimir/promptproof-action@v0
  with:
    config: promptproof.yaml
```

### Warning Mode (Non-blocking)

```yaml
- uses: geminimir/promptproof-action@v0
  with:
    config: promptproof.yaml
    mode: warn
```

### Custom Output Format

```yaml
- uses: promptproof/action@v0
  with:
    config: promptproof.yaml
    format: junit
```

### With Matrix Testing

```yaml
strategy:
  matrix:
    suite: [support, sales, docs]
steps:
  - uses: geminimir/promptproof-action@v0
    with:
      config: promptproof-${{ matrix.suite }}.yaml
```

## PR Comments

The action automatically comments on PRs with:
- Violation summary grouped by check type
- Key metrics (cost, latency, pass/fail counts)
- Expandable details for each violation type

## Artifacts

Reports are uploaded as artifacts and retained for 30 days:
- HTML report for human review
- JSON report for programmatic access
- JUnit XML for test result visualization

## License

MIT
