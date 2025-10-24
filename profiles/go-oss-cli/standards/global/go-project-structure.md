## Go project structure for CLI tools

- **Single binary output**: CLI tools should compile to a single executable binary
- **Main package location**: Place `main.go` in `cmd/[toolname]/main.go` for non-trivial projects, or root for simple tools
- **Internal package**: Use `internal/` for private packages that shouldn't be imported by external projects
- **Package naming**: Use lowercase, single-word package names (avoid underscores)
- **Go modules**: Always use Go modules (`go.mod` and `go.sum`)
- **Project root files**: Include `go.mod`, `go.sum`, `README.md`, `LICENSE`, `.gitignore`
- **Dependency management**: See dependency-management.md for `go mod tidy`, vendoring, and versioning practices
- **Build artifacts**: Add `dist/`, built binaries, and `.goreleaser.yml` output to `.gitignore`
- **Example directory**: Consider `examples/` for usage examples if CLI is complex
