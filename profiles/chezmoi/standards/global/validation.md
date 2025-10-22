## Validation best practices for chezmoi projects

- **Validate Template Syntax**: Test templates with `chezmoi execute-template` before committing
- **Dry Run Before Apply**: Always use `chezmoi diff` or `chezmoi apply --dry-run` to preview changes
- **ShellCheck Validation**: Run shellcheck on all shell scripts before committing
- **Template ShellCheck**: Run shellcheck-templates.sh on all .sh.tmpl files with preprocessing before committing
- **Mandatory Error Handling**: Verify all scripts start with `set -eo pipefail`
- **BATS Test Validation**: Run BATS test suite before committing changes
- **Fail Early**: Validate prerequisites (commands, files, permissions) at start of scripts
- **Input Validation**: Check and validate all user inputs and template data
- **Platform Detection**: Validate OS and distribution before running platform-specific code
- **Command Availability**: Check if commands exist before using them with `command -v`
- **File Existence**: Use `stat` in templates or test in scripts before referencing files
- **Template Data**: Validate that required template data exists before using it
- **Sanitize Inputs**: Sanitize user inputs in scripts to prevent injection attacks
- **Test Multiple Scenarios**: Validate templates work with different setup types (basic, minimal, developer)
- **Pre-commit Hooks**: Use git hooks to validate markdown, shell scripts, and templates automatically
- **CI/CD Validation**: Ensure CI pipeline validates all changes before merging
- **Permission Validation**: Verify files have correct permissions after chezmoi apply
