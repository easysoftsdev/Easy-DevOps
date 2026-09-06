# Versioning Strategy

This document defines the versioning policy for Easy DevOps.

## Version Format

We follow [Semantic Versioning](https://semver.org/) (SemVer):

```
MAJOR.MINOR.PATCH[-PRERELEASE][+BUILD]
```

| Component | Description | Example |
|-----------|-------------|---------|
| MAJOR | Breaking changes | 2.0.0 |
| MINOR | New features (backward-compatible) | 1.1.0 |
| PATCH | Bug fixes (backward-compatible) | 1.0.1 |
| PRERELEASE | Pre-release identifier | 1.0.0-beta.1 |
| BUILD | Build metadata | 1.0.0+build.123 |

## Version Rules

### MAJOR Version (X.0.0)

Increment MAJOR when:
- Breaking CLI command changes
- Removing deprecated features
- Changing configuration file format
- Modifying API response structures
- Changing plugin API
- Requiring newer Go version

**Examples**:
- `v1.0.0` → `v2.0.0`
- Removed `--legacy` flag
- Changed config.yaml structure

### MINOR Version (X.Y.0)

Increment MINOR when:
- Adding new CLI commands
- Adding new features
- Adding new API endpoints
- Adding new configuration options
- Adding new plugins
- New backward-compatible functionality

**Examples**:
- `v1.0.0` → `v1.1.0`
- Added `easydev disk` command
- Added WhatsApp notifications

### PATCH Version (X.Y.Z)

Increment PATCH when:
- Fixing bugs
- Security patches
- Performance improvements
- Documentation updates
- Dependency updates (non-breaking)

**Examples**:
- `v1.0.0` → `v1.0.1`
- Fixed SSH timeout issue
- Updated Go dependencies

## Pre-release Versions

Format: `X.Y.Z-PRERELEASE.N`

| Tag | Purpose | Stability |
|-----|---------|-----------|
| `alpha.N` | Early development | Unstable |
| `beta.N` | Feature complete | Mostly stable |
| `rc.N` | Release candidate | Stable |

**Examples**:
```
v1.1.0-alpha.1    # First alpha release
v1.1.0-alpha.2    # Second alpha release
v1.1.0-beta.1     # First beta release
v1.1.0-beta.2     # Second beta release
v1.1.0-rc.1       # Release candidate
v1.1.0            # Stable release
```

## Version Selection

### Users

```bash
# Install specific version
easydev install v1.0.0

# Install latest stable
easydev install latest

# Install latest beta
easydev install beta

# Install latest alpha
easydev install alpha
```

### Docker

```bash
# Specific version
docker pull easysoftsdev/easy-devops:v1.0.0

# Latest stable
docker pull easysoftsdev/easy-devops:latest

# Beta
docker pull easysoftsdev/easy-devops:beta
```

## Version Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    VERSION LIFECYCLE                                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Development ──► Alpha ──► Beta ──► RC ──► Stable ──► Deprecated       │
│                                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐               │
│  │  Alpha   │  │   Beta   │  │    RC    │  │  Stable  │               │
│  │  1-3 mo  │  │  1-2 mo  │  │  1-2 wk  │  │  Active  │               │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘               │
│                                                                         │
│  Support:   Limited        Best-effort    Full        Full + Security  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Support Policy

| Version | Support Level | Duration |
|---------|---------------|----------|
| Alpha/Beta | Community | Until next release |
| Stable (latest) | Full | Until next minor |
| Stable (previous) | Security only | 6 months |
| Stable (older) | None | End of life |

**Example**:
```
v1.2.0 released (latest stable)
v1.1.x - Security only (6 months)
v1.0.x - End of life
```

## Breaking Changes

Breaking changes require:
1. MAJOR version increment
2. Migration guide in release notes
3. Deprecation notice (1 minor version before)
4. Backward-compatible alternative (when possible)

### Deprecation Process

1. **Announce**: Add deprecation notice in release notes
2. **Warn**: Show warnings in CLI output
3. **Document**: Add migration guide
4. **Remove**: Remove in next MAJOR version

**Example**:
```
v1.0.0: --legacy flag available
v1.1.0: --legacy deprecated, warning shown
v2.0.0: --legacy removed
```

## Version Commands

```bash
# Check current version
easydev version

# Check for updates
easydev update check

# Update to latest
easydev update now

# Update to specific version
easydev update now v1.1.0

# View version history
easydev update history
```

## Version in Code

### cmd/root.go

```go
var rootCmd = &cobra.Command{
    Use:     "easydev",
    Version: "1.0.0",
    // ...
}
```

### Build-time Injection

```bash
# Build with version
go build -ldflags "-X main.version=1.0.0" -o easydev .

# Or via Makefile
make build VERSION=1.0.0
```

### Runtime Detection

```go
package main

var version = "dev"

func main() {
    if version == "dev" {
        fmt.Println("Running development build")
    }
}
```

## Version Naming Conventions

### CLI Commands

| Command | Description |
|---------|-------------|
| `easydev version` | Show current version |
| `easydev update check` | Check for updates |
| `easydev update now` | Update to latest |

### Docker Tags

| Tag | Description |
|-----|-------------|
| `latest` | Latest stable release |
| `X.Y.Z` | Specific version |
| `beta` | Latest pre-release |
| `nightly` | Latest main build |

### Git Tags

| Pattern | Example |
|---------|---------|
| `vX.Y.Z` | `v1.0.0` |
| `vX.Y.Z-beta.N` | `v1.1.0-beta.1` |
| `vX.Y.Z-rc.N` | `v1.1.0-rc.1` |

## Changelog Format

Follow [Keep a Changelog](https://keepachangelog.com/) format:

```markdown
# Changelog

## [Unreleased]

## [1.1.0] - 2026-06-15

### Added
- WhatsApp notifications
- Disk management commands

### Changed
- Improved SSH connection handling

### Fixed
- Fixed backup timeout issue

### Deprecated
- `--legacy` flag (use `--modern` instead)

### Removed
- Removed deprecated `old-command`

### Security
- Updated dependencies for security patches

## [1.0.1] - 2026-05-01

### Fixed
- Fixed deployment rollback issue
- Fixed memory leak in monitoring
```

## Version Comparison

```bash
# Compare versions
easydev version --compare v1.0.0 v1.1.0

# Check if update available
easydev update check
```

## Release Notes Template

```markdown
# Release vX.Y.Z

## Highlights
- Major feature description
- Important improvement

## What's New
### Added
- Feature 1
- Feature 2

### Changed
- Change 1

### Fixed
- Bug fix 1
- Bug fix 2

### Deprecated
- Deprecated feature

### Removed
- Removed feature

### Security
- Security fix

## Breaking Changes
- Description of breaking change
- Migration guide link

## Installation

### Binary
\`\`\`bash
# Download instructions
\`\`\`

### Docker
\`\`\`bash
docker pull easysoftsdev/easy-devops:vX.Y.Z
\`\`\`

## Full Changelog
See [CHANGELOG.md](./CHANGELOG.md)
```

## Version Validation

Before release, verify:
- [ ] Version follows SemVer
- [ ] Changelog updated
- [ ] Breaking changes documented
- [ ] Migration guide available
- [ ] Git tag created
- [ ] CI/CD triggered
- [ ] Docker image built
- [ ] Binaries uploaded
