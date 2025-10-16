## Essential files to manage with chezmoi

- **Critical Configuration Files**: Version control essential dotfiles:
  - `~/.bashrc` - Bash configuration
  - `~/.zshrc` - Zsh configuration
  - `~/.profile` - Shell profile settings
  - `~/.gitconfig` - Git configuration
  - `~/.ssh/config` - SSH client configuration
  - `~/.vimrc` or `~/.config/nvim/init.lua` - Editor configuration
- **Application Configs**: Include application-specific configurations
  - `~/.config/` directory contents (following XDG Base Directory spec)
  - Editor configs (nvim, vim, emacs, etc.)
  - Terminal emulator configs (alacritty, kitty, etc.)
  - Development tool configs (tmux, git, etc.)
- **Shell Configuration Organization**:
  - Break large shell configs into modular files (`.bashrc.d/`, `.shell.d/`)
  - Use file extensions for clarity (`.zsh`, `.bash`, `.sh`)
  - Create dynamic sourcing for configuration directories
- **Files to Generally Exclude**:
  - Temporary files and caches
  - Files with machine-specific absolute paths
  - Binary files (unless absolutely necessary)
  - Large files that rarely change
  - Secrets (use password managers instead)
- **Two-Step Initialization Pattern**:
  ```bash
  chezmoi init
  chezmoi add ~/.zshrc ~/.bashrc ~/.gitconfig
  ```
- **Modular Organization**: Split configurations into focused, manageable pieces
- **Template When Needed**: Add `.tmpl` only when files need machine-specific customization
- **Start Small**: Begin with a few essential files; expand gradually
- **Document Decisions**: Comment why specific files are or aren't managed
