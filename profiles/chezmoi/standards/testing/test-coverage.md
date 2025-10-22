## Test coverage standards for chezmoi projects

- **Focus on Critical Paths**: Prioritize testing core user workflows over edge cases
- **Setup Types**: Ensure all setup types (basic, minimal, developer) are tested
- **Script Testing**: Test all installation scripts and git hooks for correct execution
- **Template Testing**: Validate template syntax and execution with different data
- **Configuration Testing**: Verify configuration files are created with correct content
- **Cross-Platform Coverage**: Test on primary target platforms (Ubuntu at minimum)
- **Container Scenarios**: Test both container and non-container environments
- **Permission Testing**: Verify executable, private, and readonly file attributes
- **Conditional Logic**: Test all branches of conditional logic in templates and scripts
- **Error Handling**: Test that scripts fail gracefully with appropriate error messages
- **Defer Edge Cases**: Don't test every edge case during development; focus on core functionality
- **Integration Over Unit**: Prefer integration tests that verify complete workflows
- **Test Infrastructure**: Test the test infrastructure itself (helpers, fixtures, setup)
- **CI/CD Workflows**: Ensure CI/CD pipeline tests match local development tests
- **Documentation Testing**: Verify documentation matches actual implementation
- **Code Coverage Tracking**: Monitor test coverage to identify untested code paths
- **Performance Benchmarking**: Track installation and application performance over time
- **End-to-End Verification**: Regularly run full setup from scratch to catch integration issues
