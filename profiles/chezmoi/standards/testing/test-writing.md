## BATS test writing standards

### Development Testing Strategy

**Docker-First Approach:**
- **Primary Testing**: Run full test suite in Docker containers (e.g., `make test` in Docker)
- **Why Docker**: Ensures safe, isolated testing without side effects on host machine
- **When to use Docker**: Integration tests, system modifications, anything requiring packages or system changes
- **Host Quick Tests**: Fast, isolated unit tests can run on host with proper isolation (see below)
- **VM Testing**: Use VMs (Vagrant) for full end-to-end system validation

**Host Testing Rules:**
- Only for quick, isolated tests that don't modify system
- Must use temp directories and XDG isolation (see XDG_CONFIG_HOME below)
- Must use `chezmoi apply --source/--destination` isolation pattern
- Never run actual `chezmoi init` or `chezmoi apply` on host

### Test Organization and Best Practices

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
- **Chezmoi Apply Isolation**: Use `chezmoi apply --source <test-dir> --destination <test-dir>` for safe host testing
  - **When SAFE to use on host**: File permissions, template rendering, ignore patterns, file content, version checks
  - **When NOT safe (use VM/Docker instead)**: System modifications, package installs, service management, DNS config, anything requiring root
  - **Pattern**: Create temp source/dest directories, copy chezmoi files to source, apply to dest, verify results
  - **Example**: `chezmoi apply --source "$TEST_SOURCE_DIR" --destination "$TEST_DEST_DIR"`
  - **Benefit**: Fast, isolated testing without risking host system or requiring VM/Docker for file-only operations
- **Helper Functions**: Create reusable helper functions for common test operations
- **Skip When Appropriate**: Use `skip` for tests that require specific environments or tools
- **Fast Execution**: Keep tests fast; avoid network calls and heavy operations when possible
- **Clear Failure Messages**: Use descriptive failure messages to quickly identify issues
- **Exit Code Validation**: Always check exit codes for commands: `run command; [ "$status" -eq 0 ]`
