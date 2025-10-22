## Chezmoi template best practices

- **Template Naming**: Use `.tmpl` suffix for all chezmoi template files (e.g., `.bashrc.tmpl`, `script.sh.tmpl`)
- **Shebang for Script Templates**: Script templates (.sh.tmpl) must start with shebang on line 1
- **Editor Modeline for Templates**: Add vim modeline comment to help editors syntax highlight properly
  - For bash templates: `# vim: set ft=bash.gotexttmpl:`
  - For zsh templates: `# vim: set ft=zsh.gotexttmpl:`
  - For shell templates: `# vim: set ft=sh.gotexttmpl:`
  - Place modeline after shebang and before other code
- **Example Script Template Header**:
  ```bash
  #!/usr/bin/env bash
  # vim: set ft=bash.gotexttmpl:

  set -eo pipefail
  ```
- **Whitespace Control**: Use `{{- ` and ` -}}` to control whitespace and prevent extra blank lines
- **Variable Access**: Access chezmoi data using dot notation: `{{ .chezmoi.username }}`, `{{ .chezmoi.hostname }}`
- **Template Functions**: Use built-in chezmoi functions appropriately:
  - `promptString` - Interactive prompts for setup data
  - `stat` - Check file existence and attributes
  - `lookPath` - Find executables in PATH
  - `include` - Include content from other files
  - `sha256sum` - Generate hashes for change detection
  - `onepasswordRead` - Read secrets from 1Password
  - `awsSecretsManager` - Retrieve AWS secrets
- **Shared Templates**: Create reusable templates in `.chezmoitemplates/` directory
  - Reference with `{{ include "template-name" }}`
  - Use for common patterns shared across multiple files
- **Conditional Logic**: Use `{{ if condition }}...{{ end }}` for platform-specific or conditional content
- **Comments**: Use `{{/* comment */}}` for template comments that won't appear in output
- **Default Values**: Always provide sensible defaults using `promptString "prompt" "default"` or `{{ .value | default "fallback" }}`
- **Complex Logic in .chezmoi.toml.tmpl**: Put complex data structures and logic in `.chezmoi.toml.tmpl` for reuse
- **Template Execution Order**: Remember `.chezmoi.toml.tmpl` is executed first and provides data to other templates
- **String Functions**: Use template functions like `join`, `split`, `trim` for string manipulation
- **Data Types**: Be aware of data types (strings, bools, lists, maps) and use appropriate functions
- **Safe File Operations**: Use `stat` to check file existence before referencing files
