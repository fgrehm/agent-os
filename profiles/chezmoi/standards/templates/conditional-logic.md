## Conditional logic standards for templates

- **Platform Detection**: Use `{{ if eq .chezmoi.os "linux" }}` for OS-specific content
- **Distribution Detection**: Check `{{ .chezmoi.osRelease.id }}` for Linux distribution-specific logic
- **Container Detection**: Use `{{ if .virt.container }}` to handle container environments differently
- **Feature Flags**: Use component/feature flags in `.chezmoi.toml.tmpl` to enable/disable functionality
- **Setup Type Patterns**: Use `promptString` to create different setup profiles (basic, minimal, developer)
- **Nested Conditionals**: Keep nesting depth reasonable (max 2-3 levels) for readability
- **Boolean Logic**: Use `and`, `or`, `not` for complex conditions: `{{ if and .condition1 .condition2 }}`
- **Existence Checks**: Use `{{ if hasKey .data "key" }}` to check if keys exist before accessing
- **String Comparisons**: Use `eq`, `ne`, `contains` for string comparisons in conditions
- **Meaningful Structure**: Organize conditional blocks logically (OS first, then distribution, then features)
- **Comment Complex Conditionals**: Add `{{/* comments */}}` explaining complex conditional logic
- **Default Fallbacks**: Always provide `{{ else }}` fallback for critical conditionals
