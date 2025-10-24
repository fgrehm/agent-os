## Shell completion support

- **Auto-generate completions**: Use cobra's built-in completion generation (`cmd.GenBashCompletion()`)
- **Support multiple shells**: Generate completions for bash, zsh, fish, and PowerShell
- **Completion subcommand**: Provide `completion` subcommand (e.g., `mytool completion bash`)
- **Installation instructions**: Document how to install completions in README
- **Dynamic completions**: Use `ValidArgsFunction` for context-aware completions (files, API resources, etc.)
- **Flag value completion**: Provide completion for flag values when applicable (e.g., `--format json|yaml`)
- **Hidden commands**: Mark completion commands as hidden if you want them available but not in help text
- **Test completions**: Manually test completions in each shell before release
- **Homebrew integration**: Homebrew can install completions automatically if configured in formula
