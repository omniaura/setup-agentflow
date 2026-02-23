# setup-agentflow

GitHub Action that runs [agentflow](https://github.com/omniaura/agentflow) and opens a PR if generated files change.

Zero config. One step. Auto-detects `go tool af` vs `af` from your `go.mod`.

## Quick Start

```yaml
name: Agentflow
on:
  push:
    branches: [main]

permissions:
  contents: write
  pull-requests: write

jobs:
  generate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: omniaura/setup-agentflow@v1
```

That's it. This will:
1. Install agentflow (detects `go tool` vs `go install` automatically)
2. Run `af gen prompts .` on your repo
3. If generated files changed → open a PR with the diff

## Usage

### Fail instead of opening a PR

Use `create-pr: 'false'` in your PR CI to just fail if generated files are out of date:

```yaml
- uses: omniaura/setup-agentflow@v1
  with:
    create-pr: 'false'
```

### Custom directory

```yaml
- uses: omniaura/setup-agentflow@v1
  with:
    directory: './prompts'
```

### Custom command

```yaml
- uses: omniaura/setup-agentflow@v1
  with:
    command: 'af gen prompts ./src/agents'
```

### Specific version

```yaml
- uses: omniaura/setup-agentflow@v1
  with:
    version: '0.5.3'
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `version` | Agentflow version to install | `latest` |
| `directory` | Directory to run `af gen` on | `.` |
| `command` | Custom command (overrides `directory`) | |
| `create-pr` | Open a PR on changes, or fail | `true` |
| `pr-title` | PR title | `chore: update agentflow generated files` |
| `pr-branch` | PR branch name | `agentflow/update-generated` |
| `go-version` | Go version if not already installed | `stable` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | Installed agentflow version |
| `tool-mode` | `go-tool` or `go-install` |
| `changed` | `true` or `false` |
| `pr-url` | URL of created PR (if any) |

## How it works

1. Checks `go.mod` for agentflow tool dependency → uses `go tool af` if found
2. Otherwise installs via `go install github.com/omniaura/agentflow/cmd/af@<version>`
3. Runs `af gen prompts <directory>` (or your custom command)
4. If files changed and `create-pr` is `true` → commits, pushes a branch, opens a PR
5. If files changed and `create-pr` is `false` → fails the workflow

## License

Apache-2.0
