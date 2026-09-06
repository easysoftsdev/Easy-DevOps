# Contributor Guide

Welcome to Easy DevOps! This guide will help you get started as a contributor.

## Code of Conduct

We are committed to providing a welcoming and inclusive experience for everyone. Please be respectful and constructive in all interactions.

## How to Contribute

### Reporting Bugs

Before creating a bug report:
1. Check existing [issues](https://github.com/easysoftsdev/easy-devops/issues)
2. Try the latest version
3. Collect reproduction steps

When creating a bug report, include:
- Go version (`go version`)
- OS and architecture
- Steps to reproduce
- Expected vs actual behavior
- Relevant logs or screenshots

### Suggesting Features

1. Check existing discussions
2. Open a new issue with the `feature-request` label
3. Describe the use case and proposed solution
4. Wait for maintainer feedback before implementing

### Contributing Code

#### 1. Fork & Clone

```bash
# Fork on GitHub, then:
git clone https://github.com/YOUR_USERNAME/easy-devops.git
cd easy-devops
git remote add upstream https://github.com/easysoftsdev/easy-devops.git
```

#### 2. Create a Branch

```bash
# Sync with upstream
git fetch upstream
git checkout -b feature/my-feature upstream/main
```

Branch naming conventions:
| Prefix | Purpose | Example |
|--------|---------|---------|
| `feature/` | New features | `feature/backup-encryption` |
| `fix/` | Bug fixes | `fix/ssh-timeout` |
| `docs/` | Documentation | `docs/update-readme` |
| `refactor/` | Code refactoring | `refactor/extract-monitor` |
| `test/` | Adding tests | `test/deploy-unit-tests` |
| `chore/` | Maintenance | `chore/update-deps` |

#### 3. Make Changes

Follow the coding standards:
- `gofmt` for formatting
- `golangci-lint` for linting
- Conventional Commits for messages

#### 4. Test Your Changes

```bash
# Run all tests
make test

# Run linter
make lint

# Verify build
make build
```

#### 5. Commit

```bash
git add .
git commit -m "feat(deploy): add rollback confirmation prompt

- Add interactive confirmation before rollback
- Add --yes flag to skip confirmation
- Closes #123"
```

#### 6. Push & Create PR

```bash
git push origin feature/my-feature
```

Then create a Pull Request on GitHub.

## Pull Request Guidelines

### PR Checklist

- [ ] Code follows project style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No new warnings from `go vet` or `golangci-lint`
- [ ] All tests pass
- [ ] Commit messages follow Conventional Commits

### PR Title Format

```
<type>(<scope>): <description>
```

Examples:
- `feat(backup): add encryption support`
- `fix(ssh): handle connection timeout`
- `docs(readme): update installation guide`
- `refactor(monitor): extract metrics collector`

### What to Expect

- Review within 48 hours
- Maintainer feedback on design decisions
- May request changes before merge
- Squash merge for clean history

## Development Setup

See [Developer Experience Guide](./DEVELOPER_EXPERIENCE.md) for detailed setup instructions.

## Code Review Process

1. **Automated checks** run on every PR
2. **Maintainer review** within 48 hours
3. **Feedback** provided as review comments
4. **Approval** required before merge
5. **Squash merge** to maintain clean history

## Release Process

See [Release Pipeline](./RELEASE_PIPELINE.md) for details on how releases are created.

## Getting Help

- **Issues**: For bugs and feature requests
- **Discussions**: For questions and ideas
- **Discord**: For real-time chat
- **Email**: support@easysofts.net

## Recognition

Contributors are recognized in:
- Release notes
- Contributors page
- Annual contributor appreciation

Thank you for contributing to Easy DevOps!
