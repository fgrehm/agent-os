## Logging best practices for CLI tools

- **Use slog**: Prefer `log/slog` (Go 1.21+) for structured logging over `log` package
- **Log to stderr**: Write logs to stderr, reserve stdout for primary command output
- **Log levels**: Support standard levels (debug, info, warn, error) via `--log-level` flag
- **Structured logging**: Use structured key-value logging for machine-readable logs
- **JSON mode**: Provide `--log-format json` option for production/automation scenarios
- **Human-friendly default**: Default to human-readable text format for interactive use
- **Contextual logging**: Include relevant context (user, operation, resource ID) in log entries
- **No sensitive data**: Never log passwords, tokens, or sensitive user data
- **Debug flag**: Provide `-v`/`--verbose` or `--debug` for detailed troubleshooting
- **Quiet mode**: Respect `--quiet` flag by suppressing info/debug logs
- **Error visibility**: Always show error-level logs unless in quiet mode
- **Timestamps**: Include timestamps in logs, use ISO8601 format
