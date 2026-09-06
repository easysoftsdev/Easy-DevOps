# Release Pipeline

This document describes the release process for Easy DevOps.

## Release Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    RELEASE PIPELINE                                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Developer ──► PR ──► Review ──► Merge to main ──► CI ──► Release       │
│                                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │
│  │   Code   │  │  Review  │  │  Build   │  │  Test    │  │ Publish  │ │
│  │  Change  │  │  & QA    │  │  & Lint  │  │  Suite   │  │ & Deploy │ │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Release Types

| Type | Trigger | Description | Example |
|------|---------|-------------|---------|
| Patch | Bug fixes | Backward-compatible fixes | v1.0.0 → v1.0.1 |
| Minor | New features | Backward-compatible features | v1.0.0 → v1.1.0 |
| Major | Breaking changes | Incompatible API changes | v1.0.0 → v2.0.0 |

## Release Process

### 1. Prepare Release

```bash
# Ensure all tests pass
make test

# Ensure linter passes
make lint

# Update version in cmd/root.go
# Update CHANGELOG.md manually or via tool
```

### 2. Create Release Branch

```bash
# For patch releases
git checkout -b release/v1.0.1 main

# For minor releases
git checkout -b release/v1.1.0 main

# For major releases
git checkout -b release/v2.0.0 main
```

### 3. Update Version

Update `cmd/root.go`:
```go
rootCmd.Version = "X.Y.Z"
```

### 4. Update Changelog

Add to CHANGELOG.md:
```markdown
## [X.Y.Z] - YYYY-MM-DD

### Added
- Feature description

### Changed
- Change description

### Fixed
- Bug fix description

### Deprecated
- Deprecated feature

### Removed
- Removed feature

### Security
- Security fix
```

### 5. Create Commit

```bash
git add .
git commit -m "chore(release): vX.Y.Z"
```

### 6. Tag Release

```bash
git tag -a vX.Y.Z -m "Release vX.Y.Z"
```

### 7. Push

```bash
git push origin release/vX.Y.Z
git push origin vX.Y.Z
```

### 8. Create Pull Request

Create PR to merge release branch into main.

### 9. Merge & Build

After PR merge:
- GitHub Actions triggers build
- Cross-platform binaries are built
- Docker images are built and pushed

### 10. Create GitHub Release

1. Go to GitHub Releases
2. Select the tag
3. Add release notes from CHANGELOG.md
4. Attach binary artifacts
5. Publish release

## CI/CD Pipeline

### GitHub Actions Workflow

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        goos: [linux, darwin, windows]
        goarch: [amd64, arm64]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - name: Build
        run: GOOS=${{ matrix.goos }} GOARCH=${{ matrix.goarch }} go build -o easydev-${{ matrix.goos }}-${{ matrix.goarch }} .
      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: easydev-${{ matrix.goos }}-${{ matrix.goarch }}
          path: easydev-*

  release:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
      - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          files: easydev-*
          generate_release_notes: true

  docker:
    needs: release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: easysoftsdev/easy-devops:${{ github.ref_name }},easysoftsdev/easy-devops:latest
```

### Build Matrix

| Platform | Architecture | Binary Name |
|----------|--------------|-------------|
| Linux | amd64 | `easydev-linux-amd64` |
| Linux | arm64 | `easydev-linux-arm64` |
| macOS | amd64 | `easydev-darwin-amd64` |
| macOS | arm64 | `easydev-darwin-arm64` |
| Windows | amd64 | `easydev-windows-amd64.exe` |

### Docker Images

| Tag | Description |
|-----|-------------|
| `latest` | Latest stable release |
| `X.Y.Z` | Specific version |
| `nightly` | Latest main branch build |

## Release Checklist

### Pre-Release

- [ ] All tests pass (`make test`)
- [ ] Linter passes (`make lint`)
- [ ] Version updated in `cmd/root.go`
- [ ] CHANGELOG.md updated
- [ ] Documentation updated
- [ ] Breaking changes documented

### Release

- [ ] Release branch created
- [ ] Version tag created
- [ ] PR created and reviewed
- [ ] PR merged to main
- [ ] CI/CD pipeline completed
- [ ] Binaries uploaded to GitHub Release
- [ ] Docker images pushed to registry

### Post-Release

- [ ] Release notes published
- [ ] Announcement sent (Discord, Twitter)
- [ ] Documentation site updated
- [ ] Package managers notified (if applicable)

## Hotfix Process

For critical bugs in production:

```bash
# Create hotfix branch from release tag
git checkout -b hotfix/v1.0.2 v1.0.1

# Apply fix
git add .
git commit -m "fix: critical bug description"

# Tag and push
git tag -a v1.0.2 -m "Hotfix v1.0.2"
git push origin hotfix/v1.0.2
git push origin v1.0.2

# Create PR to main
# After merge, also merge back to any active release branches
```

## Release Schedule

| Release Type | Frequency | Approval Required |
|--------------|-----------|-------------------|
| Patch | As needed | 1 maintainer |
| Minor | Monthly | 1 maintainer |
| Major | Quarterly | 2 maintainers |

## Rollback Procedure

If a release has critical issues:

1. **Immediate**: Revert the release tag
   ```bash
   git tag -d vX.Y.Z
   git push origin :refs/tags/vX.Y.Z
   ```

2. **Short-term**: Create hotfix release
   ```bash
   git checkout -b hotfix/vX.Y.Z+1 vX.Y.Z-1
   # Apply fix
   ```

3. **Communication**: Notify users via:
   - GitHub Release notes
   - Discord announcement
   - Twitter/X post

## Distribution Channels

| Channel | Update Method |
|---------|---------------|
| GitHub Releases | Manual download |
| Docker Hub | `docker pull` |
| Homebrew | `brew upgrade` |
| APT | `apt upgrade` |
| YUM | `yum update` |
| Scoop | `scoop update` |
| Binary | Auto-update or manual |

## Signing Releases

For enhanced security, releases are signed:

```bash
# Import GPG key
gpg --import easydev-signing-key.asc

# Verify release
gpg --verify easydev-linux-amd64.sig easydev-linux-amd64
```

## Release Metrics

Track release success via:
- Build success rate
- Test pass rate
- Deployment success rate
- User adoption rate
- Issue reports post-release
