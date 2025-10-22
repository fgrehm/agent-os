## Dotfiles standards and best practices

- **Additive Approach**: DO NOT replace system dotfiles like .bashrc or .zshrc - use an additive approach instead
- **Add, Don't Replace**: Preserve system defaults and add custom configuration
- **Modular Directories**: Create directories like `.bashrc.d/`, `.shell.d/`, `.zshrc.d/` for modular configs
- **Source Pattern**: Add sourcing statements to existing dotfiles to load custom configurations
- **Example Pattern for .bashrc**:
  ```bash
  # Source custom configurations from .bashrc.d/
  if [ -d "$HOME/.bashrc.d" ]; then
    for file in "$HOME/.bashrc.d"/*.sh; do
      [ -r "$file" ] && source "$file"
    done
  fi
  ```
- **Template Approach**: Use modify_ scripts in chezmoi to add sourcing statements to existing files
- **Idempotency**: Ensure sourcing statements are only added once (check if already present)
- **Modular Structure**: Break large dotfiles into modular pieces loaded from subdirectories
- **Shell Configs**: Keep shell-specific configs separate (.bashrc, .zshrc) but share common logic via sourced files
- **Naming Convention**: Use `dot_` prefix for dotfiles in chezmoi source (e.g., `dot_bashrc` becomes `.bashrc`)
- **Environment Variables**: Centralize environment variables in `.exports` or similar, sourced by shells
- **Aliases and Functions**: Keep aliases in `.aliases`, functions in `.functions` or `.shell.d/`
- **Sensitive Data**: Never commit secrets; use password manager integration or environment variables
- **Cross-Shell Compatibility**: Make configs work across bash and zsh when possible
- **Conditional Loading**: Use conditionals to load configs only when appropriate (e.g., check if commands exist)
- **Performance**: Lazy-load expensive operations; don't slow down shell startup
- **Comments**: Document non-obvious configurations and why they exist
- **Backup Originals**: Before overwriting system defaults, understand what they do
- **Version Control Friendly**: Keep configs readable and well-organized for git diffs
