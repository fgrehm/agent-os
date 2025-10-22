## Error handling standards for shell scripts

- **MANDATORY: set -eo pipefail**: EVERY shell script MUST start with `set -eo pipefail` - this is non-negotiable
- **Fail Fast**: `set -e` exits immediately on any command failure
- **Pipeline Failures**: `set -o pipefail` catches failures in the middle of pipelines
- **Combined Form**: Use `set -eo pipefail` as the first substantive line after the shebang
- **Undefined Variables**: Consider `set -u` to catch undefined variable usage (but use carefully with chezmoi templates)
- **Error Messages**: Print clear, actionable error messages to stderr using `echo "Error: message" >&2`
- **Cleanup on Exit**: Use `trap` to ensure cleanup happens even on errors
- **Graceful Degradation**: When appropriate, allow scripts to continue with warnings instead of failing
- **Check Prerequisites**: Verify required commands, files, and permissions before proceeding
- **Validate Inputs**: Check function arguments and user inputs before using them
- **Command Exit Codes**: Check exit codes explicitly for critical operations using `if ! command; then`
- **Meaningful Exit Codes**: Use specific exit codes for different error conditions (1-general, 2-misuse, etc.)
- **Error Context**: Include context in error messages (what failed, where, and why)
- **Silent Failures**: Never use `|| true` to silence errors unless absolutely necessary and well-documented
