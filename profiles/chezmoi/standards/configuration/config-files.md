## Configuration file standards

- **Format Consistency**: Use appropriate formats (YAML for complex configs, TOML for simple, JSON for compatibility)
- **Validation**: Validate configuration syntax before applying (use linters, parsers)
- **Comments**: Include comments explaining non-obvious configuration choices
- **Defaults**: Provide sensible defaults; don't require users to configure everything
- **Application Configs**: Use `~/.config/app` structure following XDG Base Directory specification
- **Symlinks vs Copies**: Use symlinks (default chezmoi behavior) for configs that may be edited manually
- **File Permissions**: Set appropriate permissions (644 for configs, 600 for sensitive configs)
- **Backup Strategy**: Know which configs are safe to replace vs which need merging
- **Testing**: Test configs in isolation before applying to avoid breaking applications
- **Documentation**: Document what each configuration file does and where it should live
- **Conditional Configs**: Use templates to handle different system configurations
- **Update Strategy**: Plan how configs will be updated across multiple machines
