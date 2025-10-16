## Template data management standards

- **Single Source of Truth**: Centralize configuration data in `.chezmoi.toml.tmpl` or `.chezmoidata.yaml`
- **Data File Options**:
  - `.chezmoi.toml.tmpl` - For computed/dynamic data (can use Go templates, runs first)
  - `.chezmoidata.yaml` - For static data (simple key-value, no templates)
  - `.chezmoidata.json` - Alternative static data format
- **Choose Data Format Based on Needs**:
  - Use `.chezmoi.toml.tmpl` when you need conditionals, functions, or computed values
  - Use `.chezmoidata.yaml` for simple, static configuration data
- **Centralize Variables**: Define reusable variables in data files instead of repeating them in templates
- **Environment-Specific Data**: Use data files to handle differences between machines, OSes, or environments
- **Minimize Machine Differences**: Use templating to minimize actual differences between machine configurations
- **Work vs Personal Separation**: Use different data values or feature flags to separate work/personal configs
- **Data Structure**: Organize data hierarchically (components, features, system-specific)
- **Example Data Structure**:
  ```yaml
  # .chezmoidata.yaml
  components:
    editors:
      nvim: true
      cursor: false
    security:
      onepassword: true
  features:
    docker: true
    vagrant: false
  ```
- **Access in Templates**: Reference data with `{{ .componentname.feature }}` syntax
- **Document Data Schema**: Comment data files explaining what each section controls
