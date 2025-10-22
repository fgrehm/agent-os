## Conditional logic standards for templates

### Multi-Environment Patterns

**Target Environments:**
- **Bare-metal (Laptop)**: Full development environment with all tools and configurations
- **VM**: Testing environment for validating fresh installations
- **Container (Devcontainer)**: Subset of laptop configs for container-based development

**Environment Detection Pattern:**
```toml
# .chezmoi.toml.tmpl
[data.environment]
{{- $isContainer := or (stat "/.dockerenv") (env "container") }}
{{- $isVM := or (stat "/vagrant") (eq (env "VAGRANT") "1") }}
type = "{{ if $isContainer }}container{{ else if $isVM }}vm{{ else }}bare-metal{{ end }}"
is_container = {{ $isContainer }}
is_vm = {{ $isVM }}
is_bare_metal = {{ not (or $isContainer $isVM) }}
```

**Usage in Templates:**
```bash
# Install heavy development tools only on bare-metal
{{ if .environment.is_bare_metal }}
  # Full IDE, Docker, VMs, etc.
{{ end }}

# Minimal setup for containers
{{ if .environment.is_container }}
  # Basic shell, git, essential tools only
{{ end }}
```

### Standard Conditional Patterns

- **Platform Detection**: Use `{{ if eq .chezmoi.os "linux" }}` for OS-specific content
- **Distribution Detection**: Check `{{ .chezmoi.osRelease.id }}` for Linux distribution-specific logic
- **Container Detection**: Use environment detection pattern above (more reliable than single check)
- **Feature Flags**: Use component/feature flags in `.chezmoi.toml.tmpl` to enable/disable functionality
- **Setup Type Patterns**: Use `promptString` to create different setup profiles (basic, minimal, developer)
- **Nested Conditionals**: Keep nesting depth reasonable (max 2-3 levels) for readability
- **Boolean Logic**: Use `and`, `or`, `not` for complex conditions: `{{ if and .condition1 .condition2 }}`
- **Existence Checks**: Use `{{ if hasKey .data "key" }}` to check if keys exist before accessing
- **String Comparisons**: Use `eq`, `ne`, `contains` for string comparisons in conditions
- **Meaningful Structure**: Organize conditional blocks logically (OS first, then distribution, then features)
- **Comment Complex Conditionals**: Add `{{/* comments */}}` explaining complex conditional logic
- **Default Fallbacks**: Always provide `{{ else }}` fallback for critical conditionals
