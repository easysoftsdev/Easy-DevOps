# Developer Experience

This guide covers everything you need to know to develop, test, and contribute to Easy DevOps efficiently.

## Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| Go | 1.22+ | Core language |
| Make | 4.0+ | Build automation |
| Git | 2.30+ | Version control |
| Docker | 24.0+ | Container testing |
| golangci-lint | latest | Linting |
| air | latest | Hot-reload (optional) |

## First-Time Setup

```bash
# 1. Clone the repository
git clone https://github.com/easysoftsdev/easy-devops.git
cd easy-devops

# 2. Install dependencies
make deps

# 3. Verify installation
make verify

# 4. Run tests
make test

# 5. Build the binary
make build
```

## Project Structure

```
easy-devops/
├── cmd/                  # CLI command definitions (Cobra)
│   ├── root.go           # Root command & subcommand registration
│   ├── deploy.go         # Deployment commands
│   ├── backup.go         # Backup commands
│   ├── docker.go         # Docker management
│   ├── monitor.go        # Monitoring commands
│   ├── nginx.go          # Nginx automation
│   ├── ssh.go            # SSH management
│   └── ...
├── internal/             # Private application logic
│   ├── backup/           # Backup engine
│   ├── deploy/           # Deployment engine
│   ├── docker/           # Docker integration
│   ├── logs/             # Log aggregation
│   ├── monitoring/       # Metrics collection
│   ├── nginx/            # Nginx management
│   ├── notifications/    # Telegram/WhatsApp
│   ├── pm2/              # PM2 process manager
│   ├── scheduler/        # Cron scheduler
│   ├── ssh/              # SSH client
│   └── ssl/              # SSL certificate management
├── configs/              # Configuration files
├── deployments/          # Deployment manifests
├── scripts/              # Helper scripts
├── web/                  # Frontend (React dashboard)
├── docs/                 # Documentation
├── go.mod                # Go module definition
├── go.sum                # Dependency checksums
├── main.go               # Entry point
└── Makefile              # Build automation
```

## Development Workflow

### Daily Development

```bash
# Start hot-reload development
make dev

# Run in another terminal
easydev start --port 8081
```

### Building

```bash
# Build for current platform
make build

# Build for all platforms
make build-all

# Build with version info
make build VERSION=1.2.3

# Cross-compile
make build-linux-amd64
make build-darwin-arm64
make build-windows-amd64
```

### Testing

```bash
# Run all tests
make test

# Run with coverage
make coverage

# Run specific package tests
go test ./internal/deploy/...
go test ./internal/backup/... -v

# Run integration tests
make test-integration

# Race condition detection
go test -race ./...

# Benchmarks
go test -bench=. ./...
```

### Linting & Formatting

```bash
# Run linter
make lint

# Auto-fix formatting
make fmt

# Vet code
make vet

# Check all (fmt + vet + lint)
make check
```

## IDE Setup

### VS Code

Recommended extensions:
- `golang.go` - Go language support
- `ms-vscode.makefile-tools` - Makefile support
- `EditorConfig.EditorConfig` - Code style consistency

`.vscode/settings.json`:
```json
{
  "go.useLanguageServer": true,
  "go.lintTool": "golangci-lint",
  "go.lintFlags": ["--fast"],
  "go.testTimeout": "120s",
  "editor.formatOnSave": true,
  "[go]": {
    "editor.defaultFormatter": "golang.go"
  }
}
```

### GoLand

- Import project settings from `.idea/` if available
- Enable Go modules support
- Configure Run configurations for `main.go`

## Adding a New Command

1. Create a new file in `cmd/` (e.g., `cmd/mycommand.go`)

```go
package cmd

import (
    "fmt"
    "github.com/spf13/cobra"
)

var myCommandCmd = &cobra.Command{
    Use:   "mycommand",
    Short: "Description of my command",
    Long:  `Detailed description of my command`,
    Run: func(cmd *cobra.Command, args []string) {
        fmt.Println("Executing mycommand...")
    },
}

func init() {
    rootCmd.AddCommand(myCommandCmd)
}
```

2. Add internal logic in `internal/mycommand/`

3. Add tests in `cmd/mycommand_test.go`

4. Update `docs/DOCUMENTATION_STRUCTURE.md` with the new command

## Adding a New Feature Module

1. Create package in `internal/myfeature/`
2. Define interfaces for testability
3. Add unit tests alongside implementation
4. Wire into `cmd/` layer
5. Add integration tests

## Debugging

```bash
# Build with debug symbols
make build-debug

# Run with debug logging
EASY_DEVOPS_LOG_LEVEL=debug easydev start

# Enable pprof profiling
EASY_DEVOPS_PPROF=true easydev start
# Access at http://localhost:6060/debug/pprof/

# Delve debugger
dlv exec ./bin/easydev -- start
```

## Environment Variables for Development

| Variable | Default | Description |
|----------|---------|-------------|
| `EASY_DEVOPS_LOG_LEVEL` | `info` | Log level (debug/info/warn/error) |
| `EASY_DEVOPS_PPROF` | `false` | Enable pprof profiling |
| `EASY_DEVOPS_CONFIG` | `~/.easydev/config.yaml` | Config file path |
| `EASY_DEVOPS_SERVER_PORT` | `8080` | Dashboard port |
| `EASY_DEVOPS_DB_DRIVER` | `sqlite` | Database driver |

## Common Tasks

### Adding a Dependency

```bash
go get github.com/some/package
go mod tidy
```

### Updating Dependencies

```bash
go get -u ./...
go mod tidy
```

### Generating Documentation

```bash
make docs
```

### Running the Full CI Pipeline Locally

```bash
make ci
```

## Performance Profiling

```bash
# CPU profile
go test -cpuprofile=cpu.prof ./...
go tool pprof cpu.prof

# Memory profile
go test -memprofile=mem.prof ./...
go tool pprof mem.prof

# View in browser
go tool pprof -http=:8081 cpu.prof
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `go build` fails | Run `go mod tidy` |
| Linter errors | Run `make fmt` then `make lint` |
| Test failures | Check `go test -v ./...` for details |
| Port in use | Change port with `--port` flag |
| Permission denied | Use `sudo` or fix file permissions |
