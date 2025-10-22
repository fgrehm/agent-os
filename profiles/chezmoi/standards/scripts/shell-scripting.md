## Shell scripting best practices

- **MANDATORY Shebang**: EVERY script must start with a shebang on line 1 - no exceptions
  - Bash scripts: `#!/usr/bin/env bash`
  - POSIX scripts: `#!/bin/sh`
  - Zsh scripts: `#!/usr/bin/env zsh`
- **MANDATORY Error Handling**: ALWAYS use `set -eo pipefail` immediately after the shebang - no exceptions
- **ShellCheck Compliance**: All scripts must pass shellcheck validation without warnings or errors
- **Template ShellCheck**: For `.sh.tmpl` files, use preprocessing to validate shell syntax (see shellcheck-templates.sh)
- **POSIX Compatibility**: Prefer POSIX-compliant shell syntax when possible; use bash-specific features only when necessary
- **Quote Variables**: Always quote variables to prevent word splitting and globbing issues (`"$variable"` not `$variable`)
- **Command Substitution**: Use modern `$(command)` syntax instead of backticks
- **Avoid Unnecessary Subshells**: Don't wrap conditions in `$(...)` - use commands directly in conditionals
- **Function Organization**: Place functions at the top of scripts, with main logic at the bottom
- **Single Responsibility**: Each script should have one clear purpose; break complex operations into separate scripts
- **Library and Execution Mode**: Design scripts to work both as executables and as sourceable libraries
- **Exit Codes**: Use meaningful exit codes (0 for success, non-zero for errors)
- **Readonly Variables**: Use `readonly` for constants and configuration that shouldn't change
- **Local Variables**: Always declare function variables as `local` to avoid polluting global scope
- **Command Availability Checks**: Use `command -v tool >/dev/null 2>&1` to check if commands exist before using them
