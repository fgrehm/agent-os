## Template safety and validation standards

- **Test Templates Before Applying**: Always use `chezmoi execute-template` to test templates before applying
- **Dry Run First**: Use `chezmoi apply --dry-run` or `chezmoi diff` to preview changes
- **Avoid Destructive Defaults**: Never default to destructive actions; require explicit user confirmation
- **Validate External Commands**: Check if commands exist using `lookPath` before using them in templates
- **Safe Script Templates**: In `.sh.tmpl` files, ensure the resulting shell script will be valid bash
- **Mandatory Error Handling**: All `.sh.tmpl` files MUST include `set -eo pipefail` after the shebang
- **ShellCheck Template Validation**: Run shellcheck-templates.sh on all `.sh.tmpl` files before committing
- **Handle Missing Data**: Use `{{ if .data }}` checks before accessing potentially undefined data
- **Escape Special Characters**: Properly escape quotes, backslashes, and other special characters in output
- **No Secrets in Templates**: Never commit secrets directly in templates; use password manager integration
- **Test with Different Data**: Test templates with various input data (minimal, basic, developer setup types)
- **Idempotent Templates**: Templates should produce the same output when run multiple times with same data
- **Version Compatibility**: Ensure templates work with the minimum required chezmoi version
- **Error Messages**: Include helpful error messages when required data is missing
