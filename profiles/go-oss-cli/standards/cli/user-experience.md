## CLI user experience standards

- **Clear error messages**: Provide actionable error messages that tell users how to fix issues
- **Example usage**: Include usage examples in help text showing common use cases
- **Helpful defaults**: Choose sensible defaults that work for most users
- **Input validation**: Validate user input early and provide clear feedback on what's wrong
- **Consistent terminology**: Use the same terms throughout commands, flags, and documentation
- **Configuration files**: Support config files (e.g., `.toolname.yml`) for repeated settings
- **Environment variables**: Allow overriding flags with environment variables (e.g., `TOOLNAME_FLAG`)
- **Interactive mode**: Consider interactive prompts as alternative to flags for complex workflows
- **Dry-run mode**: Provide `--dry-run` flag for previewing changes without executing
- **Verbose output**: Support `-v`/`--verbose` for debugging and detailed output
- **Smart suggestions**: Suggest correct command when user typos (e.g., "Did you mean 'status'?")
