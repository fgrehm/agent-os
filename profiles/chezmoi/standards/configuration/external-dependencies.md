## External dependencies management standards

- **Use .chezmoiexternal.toml**: Manage external dependencies declaratively
- **Supported External Types**:
  - `git-repo` - Clone entire git repositories
  - `archive` - Download and extract archives
  - `archive-file` - Extract specific files from archives
  - `file` - Download individual files
- **Git Repository Pattern**:
  ```toml
  [".oh-my-zsh"]
      type = "git-repo"
      url = "https://github.com/ohmyzsh/ohmyzsh.git"
      refreshPeriod = "168h"  # Update weekly
  ```
- **Archive Download Pattern**:
  ```toml
  [".local/share/nvim"]
      type = "archive"
      url = "https://github.com/neovim/neovim/releases/latest/download/nvim-{{ .chezmoi.os }}-{{ .chezmoi.arch }}.tar.gz"
      refreshPeriod = "168h"
      stripComponents = 1
  ```
- **Binary Extraction Pattern**:
  ```toml
  [".local/bin/hugo"]
      type = "archive-file"
      url = {{ gitHubLatestReleaseAssetURL "gohugoio/hugo" (printf "hugo_*_%s-%s.tar.gz" .chezmoi.os .chezmoi.arch) | quote }}
      executable = true
      path = "hugo"
      refreshPeriod = "168h"
  ```
- **Refresh Periods**: Use `refreshPeriod` to control update frequency
  - `"168h"` - Weekly updates (recommended for stable tools)
  - `"24h"` - Daily updates (for rapidly changing dependencies)
  - Omit for one-time downloads
- **Platform-Specific URLs**: Use template variables for cross-platform downloads
- **GitHub Functions**: Use built-in functions for GitHub releases
  - `gitHubLatestRelease` - Get latest release info
  - `gitHubLatestReleaseAssetURL` - Get download URL for specific asset
  - `gitHubReleases` - Get list of releases
- **Version Pinning**: Pin to specific versions for stability when needed
- **Checksum Verification**: Include checksums when available for security
- **Dependency Documentation**: Document why each external dependency is needed
- **Update Strategy**: Balance between staying current and maintaining stability
- **Testing**: Test external dependencies in containers before applying
- **Fallback Handling**: Handle cases where downloads fail gracefully
- **Cache Management**: Understand that chezmoi caches external resources
