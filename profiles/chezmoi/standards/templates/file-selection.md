## Selective file application standards

- **Ignore Patterns**: Use `.chezmoiignore` file to exclude files from being managed
  - `.chezmoiignore` is itself a template, allowing conditional ignoring
  - Example: Ignore work configs on personal machines
- **Conditional File Inclusion**: Use chezmoi attributes to selectively include files based on conditions
- **OS-Specific Files**: Use `.os` suffix to include files only on specific operating systems
  - Example: `script.sh.darwin` (macOS only), `script.sh.linux` (Linux only)
- **Hostname-Specific Files**: Use `.hostname` suffix for machine-specific configurations
  - Example: `config.yaml.work-laptop`, `config.yaml.home-desktop`
- **Template Conditionals**: Use `{{ if }}` blocks to conditionally include content within files
- **Example Pattern**:
  ```
  {{- if eq .chezmoi.os "darwin" }}
  # macOS-specific configuration
  {{- end }}
  {{- if eq .chezmoi.os "linux" }}
  # Linux-specific configuration
  {{- end }}
  ```
- **Distribution-Specific Logic**: Check `.chezmoi.osRelease.id` for Linux distribution
- **Feature Flags**: Use data variables to enable/disable entire files or sections
- **Minimize Duplication**: Use templates to share common logic instead of duplicating files
- **Clear Naming**: Use descriptive suffixes that clearly indicate when files apply
- **Document Conditions**: Comment why files are OS or host-specific
- **Test All Paths**: Test that conditionals work correctly on all target platforms
