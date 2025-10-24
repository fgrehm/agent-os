## Go dependency management

- **go mod tidy**: Run `go mod tidy` regularly to clean up unused dependencies and update `go.mod`/`go.sum`
- **Commit go.sum**: Always commit `go.sum` to version control for reproducible builds
- **Minimal dependencies**: Favor standard library over third-party packages when practical
- **Direct dependencies only**: Only add dependencies you directly import; let Go handle transitive deps
- **Version pinning**: Use specific versions in `go.mod`, avoid `latest` or branch names in production
- **Vendoring when needed**: Vendor dependencies (`go mod vendor`) only for critical reliability or offline builds
- **Vendor in .gitignore**: Generally exclude `vendor/` from git unless you have specific reasons to commit it
- **Dependency updates**: Regularly check for updates with `go list -u -m all` and test before upgrading
- **Security scanning**: Use `govulncheck` to scan for known vulnerabilities in dependencies
- **Module proxies**: Leverage Go module proxies (proxy.golang.org) for reliability and security
- **Replace directives**: Use `replace` in `go.mod` for local development only, not in released code
- **Pre-commit hook**: Consider running `go mod tidy` in pre-commit hooks to keep dependencies clean
