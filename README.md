# setup-agentflow

GitHub Action to install [agentflow](https://github.com/omniaura/agentflow) in your CI/CD pipeline.

Automatically detects how to install agentflow:
- If your `go.mod` has agentflow as a tool dependency → uses `go tool af`
- Otherwise → uses `go install`

## Usage

### Basic (latest version)

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: omniaura/setup-agentflow@v1
  - run: af gen prompts ./prompts
```

### With go.mod tool dependency (recommended)

First, add agentflow to your project:

```bash
go get -tool github.com/omniaura/agentflow/cmd/af@latest
```

Then in your workflow:

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: omniaura/setup-agentflow@v1
  - run: go tool af gen prompts ./prompts
```

### Specific version

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: omniaura/setup-agentflow@v1
    with:
      version: '0.5.3'
  - run: af gen prompts ./prompts
```

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `version` | Agentflow version to install (e.g. `0.5.3`, `latest`) | `latest` |
| `go-version` | Go version (only needed if Go is not already set up) | `stable` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | The installed agentflow version |
| `tool-mode` | How it was installed: `go-tool` or `go-install` |

## How it works

1. Ensures Go is available (sets up Go if not already installed)
2. Checks if `go.mod` exists and contains agentflow as a dependency
3. If found in go.mod → downloads the module and uses `go tool af`
4. If not found → installs via `go install github.com/omniaura/agentflow/cmd/af@<version>`
5. Verifies the installation by running `af --version`

## License

Apache-2.0
