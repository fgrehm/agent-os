## Chezmoi run script standards

### .chezmoiscripts/ Best Practice

- **Preferred Location**: Place `run_*` scripts in `home/.chezmoiscripts/` directory
- **Why**: Scripts in `.chezmoiscripts/` execute but don't create a corresponding directory in your home directory
- **Benefit**: Keeps your target home directory clean and organized
- **Example**: `home/.chezmoiscripts/run_once_10_install-packages.sh` executes but doesn't create `~/.chezmoiscripts/`
- **Alternative**: Placing scripts directly in `home/` (e.g., `home/run_once_setup.sh`) also works but creates script files in your home directory

### Script Execution Control

- **Script Execution Control**: Use chezmoi's naming prefixes to control when and how scripts run
- **Script Prefixes**:
  - `run_once_` - Runs only once per machine (tracked in chezmoi state)
  - `run_onchange_` - Re-runs when script content or dependencies change
  - `run_before_` - Runs before applying files
  - `run_after_` - Runs after applying files (most common)
- **Hash-Based Triggering**: Use hash comments in `run_onchange_` scripts to trigger on dependency changes
  - Example: `# config hash: {{ include "dot_config/tool/config.toml" | sha256sum }}`
  - Script re-runs whenever the included file changes
  - Can hash multiple dependencies in comments
- **Execution Order**: Use numeric prefixes to control script execution order
  - Example with .chezmoiscripts: `.chezmoiscripts/run_once_10_packages.sh`, `.chezmoiscripts/run_once_20_setup.sh`
  - Alternative example: `run_once_01-install-packages.sh`, `run_once_before_02-setup-homebrew.sh`
  - Use phase-based numbering: `00-bootstrap/`, `01-installers/`, `02-configurations/`
- **Template Scripts**: Add `.tmpl` suffix to make scripts respond to changing data/secrets
  - Example: `run_onchange_install-packages.sh.tmpl`
  - Changes to template variables trigger re-execution
- **One-Click Setup Pattern**: Create a bootstrap script that:
  1. Installs prerequisites (e.g., Homebrew, XCode tools)
  2. Installs chezmoi itself
  3. Runs `chezmoi init --apply`
- **Idempotency**: All scripts must be idempotent (safe to run multiple times)
- **Shebang Required**: All run scripts must start with `#!/usr/bin/env bash` (or appropriate shell)
- **Error Handling**: Always include `set -eo pipefail` immediately after shebang
- **Template Modeline**: For .tmpl scripts, add `# vim: set ft=bash.gotexttmpl:` after shebang
- **Platform Detection**: Use template variables to handle cross-platform scripts
  - `{{ .chezmoi.os }}` - Operating system (linux, darwin, etc.)
  - `{{ .chezmoi.arch }}` - Architecture (amd64, arm64, etc.)
  - `{{ .chezmoi.osRelease.id }}` - Linux distribution ID
- **Dependency Checks**: Verify required commands exist before using them
- **State Management**: Let chezmoi track state; don't create custom state files
- **Template Variables in Scripts**: Use chezmoi templates for dynamic script content
- **Exit Early**: Exit scripts early if conditions aren't met (wrong OS, missing dependencies)
- **Logging**: Provide clear output about what the script is doing
- **Testing**: Test scripts in containers/VMs before applying to main system
