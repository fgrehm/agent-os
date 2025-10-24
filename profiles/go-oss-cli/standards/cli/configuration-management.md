## Configuration management and precedence

- **Configuration hierarchy**: Follow precedence order: CLI flags > environment variables > config file > defaults
- **Config file support**: Use viper or similar for YAML/TOML/JSON config file parsing
- **Standard locations**: Look for config in `~/.config/toolname/config.yml`, `~/.toolname.yml`, `./.toolname.yml`
- **XDG Base Directory**: Respect XDG Base Directory spec on Linux (`$XDG_CONFIG_HOME`)
- **Environment variables**: Prefix env vars with tool name (e.g., `MYTOOL_API_KEY`)
- **Config file flag**: Provide `--config` flag to override default config file location
- **Config validation**: Validate config on load and provide clear error messages
- **Config generation**: Provide command to generate sample config file (e.g., `mytool config init`)
- **Merge behavior**: Deep merge config file with defaults, don't require complete config
- **Sensitive values**: Support reading secrets from env vars or separate files, not in main config
- **Config visibility**: Provide command to show effective config (e.g., `mytool config show`)
- **Per-project config**: Support project-specific config in current directory
