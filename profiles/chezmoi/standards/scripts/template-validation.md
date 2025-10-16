## Template script validation standards

- **ShellCheck on Templates**: Template shell scripts (.sh.tmpl) must pass shellcheck validation
- **Preprocessing Required**: Templates contain Go template syntax that shellcheck cannot parse directly
- **Template Preprocessing**: Use shellcheck-templates.sh or similar to preprocess templates before validation
- **Preprocessing Steps**:
  1. Remove complex template constructs (variable assignments, conditionals)
  2. Replace template variables with valid shell dummy values
  3. Handle template functions (join, quote, etc.)
  4. Clean artifacts and ensure valid shell syntax
  5. Run standard shellcheck on the processed output
- **Validation Pipeline**: Template validation should be part of pre-commit hooks and CI/CD
- **Common Template Patterns**: Document common template patterns that require special preprocessing
- **Test Both Sides**: Validate both the template syntax (chezmoi) and resulting shell script (shellcheck)
- **Integration with CI**: Ensure template validation runs automatically in CI pipeline
- **Local Validation**: Developers should run template validation locally before committing
- **Error Reporting**: Provide clear error messages showing both template and shell script issues
