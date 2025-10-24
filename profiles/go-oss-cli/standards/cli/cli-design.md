## CLI design best practices

- **Flag parsing library**: Use `cobra` or `flag` (stdlib) for command-line argument parsing
- **Subcommands**: Structure complex CLIs with subcommands (e.g., `git commit`, `docker run`)
- **Consistent naming**: Use lowercase, hyphen-separated subcommand names (`my-tool do-thing`)
- **Help text**: Provide clear, concise help text for all commands and flags
- **Short and long flags**: Support both short (`-v`) and long (`--verbose`) flag variants
- **Exit codes**: Return 0 for success, non-zero for errors (use conventional codes: 1=general error, 2=misuse)
- **STDOUT vs STDERR**: Write normal output to stdout, errors and warnings to stderr
- **Progress indicators**: Show progress for long-running operations (spinners, progress bars)
- **Color support**: Use color for improved readability but respect `NO_COLOR` env var
- **Confirmation prompts**: Require confirmation for destructive operations
- **Version flag**: Always provide `--version` flag showing current version
- **JSON output option**: Consider `--json` flag for machine-readable output
- **Quiet mode**: Support `--quiet`/`-q` flag to suppress non-essential output
