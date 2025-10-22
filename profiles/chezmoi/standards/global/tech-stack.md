## Tech stack for chezmoi dotfiles

This defines the technical stack for a chezmoi-managed dotfiles repository.

### Core Tools
- **Dotfiles Manager:** chezmoi (https://www.chezmoi.io/)
- **Shell:** Bash (with POSIX compatibility when possible)
- **Template Engine:** Go templates (built into chezmoi)
- **Version Control:** Git

### Development & Testing
- **Test Framework:** BATS (Bash Automated Testing System)
- **Shell Linter:** ShellCheck (with template preprocessing support)
- **Markdown Linter:** markdownlint-cli2
- **Package Manager:** make (for development workflows)
- **VM Testing:** Vagrant (for end-to-end system testing)
- **Container Testing:** Docker/Podman (for isolated testing)

### Quality & Validation
- **Linting/Formatting:** ShellCheck for shell scripts, markdownlint for documentation
- **Pre-commit Hooks:** Custom git hooks for validation
- **Template Testing:** chezmoi execute-template, chezmoi diff

### CI/CD
- **CI Platform:** GitHub Actions
- **Container Testing:** Docker (for devcontainer and integration tests)
- **Cross-platform Testing:** Ubuntu, Fedora, other Linux distributions

### Configuration Formats
- **TOML:** chezmoi configuration (.chezmoi.toml.tmpl)
- **YAML:** GitHub Actions workflows, application configs
- **JSON:** Application-specific configurations
- **Markdown:** Documentation, CLAUDE.md, README.md

### Target Environments
- **Primary OS:** Linux (Ubuntu, Fedora, Arch, etc.)
- **Shells:** Bash, Zsh
- **Containers:** Docker, Podman (for development and testing)
- **Desktop Environment:** GNOME (optional, for GUI configurations)

### Security & Secrets
- **Encryption:** age (for file-level encryption)
- **Password Managers:** 1Password CLI, Bitwarden CLI (for secret retrieval)
- **Secret Management:** Password manager integration (recommended) or age encryption

### External Dependencies
- **Management:** .chezmoiexternal.toml
- **Package Managers:** mise, asdf (for version management)
- **Update Strategy:** refreshPeriod-based automatic updates
