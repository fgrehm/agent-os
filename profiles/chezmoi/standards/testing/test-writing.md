## BATS test writing standards

- **Test Organization**: Group related tests in `.bats` files by component (scripts, templates, configs)
- **Setup and Teardown**: Use `setup()` and `teardown()` functions for test initialization and cleanup
- **Descriptive Test Names**: Use `@test "description"` with clear, descriptive test names
- **Assertions**: Use `[ condition ]` for assertions; check exit codes with `run` command
- **Test Isolation**: Each test should be independent; use temporary directories for file operations
- **Temp Directory Pattern**: Create unique temp directories in `./tmp/` to avoid conflicts
- **Critical Path Focus**: Test critical user workflows and core functionality first
- **Component Tests**: Validate different setup types (basic, minimal, developer) separately
- **Template Testing**: Test chezmoi templates with `chezmoi execute-template` in isolated environments
- **XDG_CONFIG_HOME Isolation**: Set `XDG_CONFIG_HOME` in tests to avoid modifying user's chezmoi config
- **Helper Functions**: Create reusable helper functions for common test operations
- **Skip When Appropriate**: Use `skip` for tests that require specific environments or tools
- **Fast Execution**: Keep tests fast; avoid network calls and heavy operations when possible
- **Clear Failure Messages**: Use descriptive failure messages to quickly identify issues
- **Exit Code Validation**: Always check exit codes for commands: `run command; [ "$status" -eq 0 ]`
