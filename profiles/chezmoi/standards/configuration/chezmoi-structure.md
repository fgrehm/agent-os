## Chezmoi Structure Standards

### Project Organization

**Directory Structure:**
```
project-root/
├── .chezmoiroot              # Points to home/ as source directory
├── home/                     # Chezmoi source directory
│   ├── .chezmoi.toml.tmpl    # Configuration template
│   ├── .chezmoidata.toml     # Static data (optional)
│   ├── .chezmoiignore        # Ignore patterns
│   ├── .chezmoiexternal.toml # External dependencies (optional)
│   ├── .chezmoiremove        # Files to remove (optional)
│   ├── .chezmoiversion       # Minimum version (optional)
│   ├── .chezmoiscripts/      # Executable scripts (preferred for run_* scripts)
│   ├── .chezmoitemplates/    # Shared templates (optional)
│   └── dot_*/                # Managed dotfiles
├── install/                  # Installation scripts
├── scripts/                  # Utility scripts
├── tests/                    # BATS tests
└── configs/                  # Actual config content (symlinked)
```

### Special Files Reference

#### `.chezmoiroot` (Project Root)
- **Location**: Project root directory
- **Purpose**: Tells chezmoi to use `home/` as the source directory
- **Content**: Single line containing `home`
- **Why**: Allows separation of chezmoi-managed files from project infrastructure
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoiroot/

#### `.chezmoi.toml.tmpl` (Source Directory)
- **Location**: `home/.chezmoi.toml.tmpl`
- **Purpose**: Dynamic configuration template, evaluated during `chezmoi init`
- **Use cases**:
  - Environment detection (VM, container, bare-metal)
  - User prompts for personal data
  - System-specific configuration
- **Features**: Supports Go templates and chezmoi template functions
- **Data section**: Exposes variables to all templates via `.variableName`
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoi-format-tmpl/

#### `.chezmoidata.toml` (Source Directory)
- **Location**: `home/.chezmoidata.toml` (or `.json`, `.yaml`, `.jsonc`)
- **Purpose**: Static data for templates
- **Use cases**: Constants, shared configuration values
- **Difference from `.chezmoi.toml.tmpl`**: Cannot be a template, purely static
- **Multiple files**: Multiple `.chezmoidata` files are merged in lexical order
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoidata-format/

#### `.chezmoiignore` (Source Directory)
- **Location**: `home/.chezmoiignore`
- **Purpose**: Specify files to ignore (not manage)
- **Syntax**:
  - Glob patterns using `doublestar.Match`
  - Comments with `#`
  - Exclusions with `!` prefix
  - Supports templates for conditional ignores
- **Use cases**:
  - README files in source directory
  - Machine-specific files
  - Temporary or backup files
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoiignore/

#### `.chezmoiexternal.toml` (Source Directory)
- **Location**: `home/.chezmoiexternal.toml` (or alternative formats)
- **Purpose**: Include external files, archives, or git repositories
- **Types**:
  - `file`: Download single file
  - `archive`: Extract entire archive
  - `archive-file`: Extract specific file from archive
  - `git-repo`: Clone/pull git repository
- **Features**: Checksum verification, refresh periods, file attributes
- **Use cases**:
  - Download tools from GitHub releases
  - Include Oh My Zsh
  - Fetch vim plugins
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoiexternal-format/

#### `.chezmoiremove` (Source Directory)
- **Location**: `home/.chezmoiremove`
- **Purpose**: Specify target files/directories to remove
- **Features**: Always interpreted as template (even without `.tmpl` extension)
- **Use cases**: Clean up obsolete configuration files
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoiremove/

#### `.chezmoiversion` (Source Directory)
- **Location**: `home/.chezmoiversion`
- **Purpose**: Specify minimum required chezmoi version
- **Content**: Semantic version number (e.g., `2.50.0`)
- **Behavior**: Prevents older chezmoi versions from processing source state
- **Use cases**: Ensure compatibility with newer chezmoi features
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoiversion/

#### `.chezmoiscripts/` (Source Directory)
- **Location**: `home/.chezmoiscripts/`
- **Purpose**: Directory for executable scripts that don't create a corresponding directory in the target state
- **Preferred location for**: `run_once_*`, `run_onchange_*`, `run_before_*`, `run_after_*` scripts
- **Benefit**: Keeps your home directory clean by not creating a `.chezmoiscripts/` directory in the target
- **Use cases**: Setup scripts, package installation, system configuration
- **Example**: `home/.chezmoiscripts/run_once_10_install-packages.sh`
- **Reference**: https://www.chezmoi.io/reference/special-files/chezmoiscripts/

#### `.chezmoitemplates/` (Source Directory)
- **Location**: `home/.chezmoitemplates/`
- **Purpose**: Shared template functions and snippets
- **Usage**: Include in templates with `{{ template "name" }}`
- **Use cases**: Reusable template logic, helper functions

### File Naming Conventions

**Attribute Prefixes:**
- `dot_` - Creates dotfile (e.g., `dot_bashrc` → `.bashrc`)
- `executable_` - Makes file executable
- `symlink_` - Creates symlink (content is target path)
- `private_` - Sets permissions to 0600
- `readonly_` - Makes file read-only
- `exact_` - Remove files not in source

**Script Prefixes:**
- `run_once_` - Execute script once
- `run_onchange_` - Execute when content changes
- `run_before_` - Execute before applying changes
- `run_after_` - Execute after applying changes

**Execution Order:**
- Use numeric prefixes: `run_once_10_setup.sh`, `run_once_20_packages.sh`
- Lower numbers execute first

**Multiple Attributes:**
- Combine with underscores: `private_dot_ssh_config`
- Order matters: attributes before name
- Example: `executable_run_once_10_install.sh`

### Processing Order

Chezmoi evaluates files in this order:
1. `.chezmoiroot` - Sets source path
2. `.chezmoi.$FORMAT.tmpl` - Prepares configuration
3. `.chezmoidata.*` files - Loads static data
4. `.chezmoitemplates/` - Loads shared templates
5. `.chezmoiignore` - Determines ignored files
6. `.chezmoiremove` - Marks files for removal
7. `.chezmoiexternal.*` - Includes external sources
8. `.chezmoiversion` - Checks version requirements
9. Regular files - Processes templates and applies

### Best Practices

**Safety:**
- NEVER run `chezmoi init` or `chezmoi apply` on development host
- Always test in Docker containers or VMs first
- Use `--dry-run` flag to preview changes before applying

**Organization:**
- Keep `.chezmoiroot` at project root
- Put all chezmoi special files in source directory (`home/`)
- Separate concerns: chezmoi files in `home/`, scripts in `scripts/`, tests in `tests/`

**Templates:**
- Use `.chezmoi.toml.tmpl` for dynamic data (environment detection, prompts)
- Use `.chezmoidata` files for static, consistent values
- Keep template logic simple and readable
- Document complex template decisions

**Version Control:**
- Commit all source files
- Ignore `.chezmoistate` (chezmoi's internal state)
- Document special file purposes in comments
- Keep special files minimal and focused

**External Dependencies:**
- Use `.chezmoiexternal.toml` for managed external files
- Include checksums for verification
- Set refresh periods appropriately
- Document why external files are needed

### Common Patterns

**Environment Detection:**
```toml
# home/.chezmoi.toml.tmpl
[data.environment]
{{- $isContainer := false }}
{{- $isVM := false }}

{{- if or (stat "/.dockerenv") (env "container") }}
{{-   $isContainer = true }}
{{- end }}

{{- if or (stat "/vagrant") (eq (env "VAGRANT") "1") }}
{{-   $isVM = true }}
{{- end }}

type = "{{ if $isContainer }}container{{ else if $isVM }}vm{{ else }}bare-metal{{ end }}"
is_container = {{ $isContainer }}
is_vm = {{ $isVM }}
is_bare_metal = {{ not (or $isContainer $isVM) }}
```

**Symlink Pattern for Externally-Modified Configs:**
```
# home/dot_config/symlink_nvim.tmpl
{{ .chezmoi.workingTree }}/configs/nvim
```
This creates `~/.config/nvim` → `<repo>/configs/nvim`, so app modifications are tracked in git.

**Conditional File Management:**
```
# home/.chezmoiignore
# Ignore work-specific configs on personal machine
{{- if ne .chezmoi.hostname "work-laptop" }}
.work-config
{{- end }}
```

### References

- Chezmoi Special Files: https://www.chezmoi.io/reference/special-files/
- Template Reference: https://www.chezmoi.io/reference/templates/
- Attribute Reference: https://www.chezmoi.io/reference/source-state-attributes/
