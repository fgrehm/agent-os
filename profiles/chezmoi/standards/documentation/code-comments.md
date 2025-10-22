## Code commenting standards

- **Explain Why, Not What**: Comments should explain reasoning, not describe obvious code
- **Script Headers**: Include purpose, usage, and prerequisites at the top of shell scripts
- **Complex Logic**: Comment complex conditionals, template logic, and non-obvious algorithms
- **TODOs**: Use `# TODO:` for future improvements; include context and owner if applicable
- **Workarounds**: Document workarounds with `# WORKAROUND:` explaining why it's needed
- **Platform-Specific Code**: Comment why platform-specific code is necessary
- **Template Comments**: Use `{{/* comment */}}` for chezmoi template comments that won't appear in output
- **Shell Comments**: Use `#` for shell script comments; place on separate line for clarity
- **Function Documentation**: Document function parameters, return values, and side effects
- **Warning Comments**: Use `# WARNING:` or `# IMPORTANT:` for critical information
- **Remove Obsolete Comments**: Delete comments when the code they describe is removed or changed
- **Avoid Redundant Comments**: Don't comment obvious code; let the code speak for itself
