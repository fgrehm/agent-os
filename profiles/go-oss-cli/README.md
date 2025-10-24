# Go OSS CLI Profile

## Purpose

Profile for building open source Go command-line tools and side projects. Focuses on Go idioms, CLI best practices, cross-platform distribution, and comprehensive testing.

## Tech Stack

- **Language**: Go (Golang)
- **CLI Framework**: cobra (or stdlib flag for simple cases)
- **Release Automation**: GoReleaser
- **Distribution**: GitHub Releases, Homebrew, Docker (optional)
- **Testing**: Go testing framework, table-driven tests

## Inherits From

`opensource-base`

Inherits OSS foundations including:
- Licensing and versioning standards
- README, CONTRIBUTING, CHANGELOG best practices
- Community templates (issues, PRs, code of conduct)
- Security policy and dependency management
- Automation (GitHub Actions, release workflows)

## Customizations

### Standards

**Global:**
- `go-project-structure.md` - Go module layout, package organization
- `go-code-style.md` - Idiomatic Go conventions, naming, error handling

**CLI:**
- `cli-design.md` - Subcommands, flags, help text, exit codes
- `user-experience.md` - Error messages, config files, interactive modes

**Distribution:**
- `cross-platform.md` - Multi-platform builds, GoReleaser configuration
- `package-managers.md` - Homebrew, Docker, installation methods

**Testing:**
- `go-testing.md` - Unit tests, table-driven tests, test coverage
- `cli-testing.md` - Integration tests, golden files, exit code verification

### Agents

- **implementer**: Specialized for Go CLI development with focus on Go idioms and open source best practices

### Commands

Inherits all Agent OS workflow commands from `opensource-base`:
- `/plan-product` - Create mission, roadmap, tech-stack
- `/shape-spec` - Shape rough ideas into requirements
- `/write-spec` - Write detailed specification (OSS-focused)
- `/create-tasks` - Break spec into task list
- `/implement-tasks` - Simple implementation workflow
- `/orchestrate-tasks` - Advanced multi-agent orchestration
- `/improve-skills` - Optimize Claude Code Skills

## Configuration

Supports both traditional standards injection and Claude Code Skills mode.

### Installation

```bash
~/agent-os/scripts/project-install.sh --profile go-oss-cli
```

### With Claude Code Skills

```bash
# In your project's config.yml, set:
# standards_as_claude_code_skills: true

~/agent-os/scripts/project-install.sh --profile go-oss-cli

# Then optimize skills:
cd your-project
/improve-skills
```

## Example Project Workflow

1. **Create new Go CLI project**:
   ```bash
   mkdir my-cli-tool && cd my-cli-tool
   git init
   go mod init github.com/user/my-cli-tool
   ```

2. **Install Agent OS profile**:
   ```bash
   ~/agent-os/scripts/project-install.sh --profile go-oss-cli
   ```

3. **Plan your project**:
   ```bash
   /plan-product
   ```

4. **Implement features**:
   ```bash
   /write-spec
   /implement-tasks
   ```

5. **Set up distribution**:
   - Configure GoReleaser (`.goreleaser.yml`)
   - Set up GitHub Actions for releases
   - Create Homebrew tap

## Key Features

- **Go Best Practices**: Idiomatic Go code following community conventions
- **CLI Excellence**: Intuitive command structure, helpful error messages, rich help text
- **Cross-Platform**: Build for Linux, macOS, Windows with single command
- **Easy Distribution**: Automated releases to GitHub, Homebrew, and other package managers
- **Comprehensive Testing**: Unit tests, integration tests, CLI-specific testing strategies
- **OSS Ready**: All the foundations for successful open source projects (inherited from opensource-base)

## Tips

- Start with stdlib `flag` for simple CLIs, graduate to `cobra` as complexity grows
- Use GoReleaser from day one - it's easy to configure and saves massive time
- Write tests as you go, especially for CLI flag parsing and output formatting
- Keep your `main()` function minimal - extract logic into testable packages
- Document installation methods prominently in your README
- Use semantic versioning and maintain a CHANGELOG
