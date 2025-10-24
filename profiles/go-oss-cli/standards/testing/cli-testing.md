## CLI testing strategies

- **Unit tests**: Test individual functions and packages in isolation
- **Integration tests**: Test CLI commands end-to-end by invoking them programmatically
- **Golden files**: Use golden file testing for comparing expected output (in `testdata/`)
- **Exit code testing**: Verify commands return correct exit codes for success/failure cases
- **Flag parsing tests**: Test that flags are parsed correctly and validated
- **STDOUT/STDERR capture**: Capture and verify output to stdout and stderr separately
- **Environment testing**: Test behavior with different environment variables
- **Temp directories**: Use `t.TempDir()` for tests that need file system operations
- **Error scenarios**: Test error paths and user-facing error messages
- **Main function testing**: Make `main()` thin and testable by extracting logic to testable functions
- **CI testing**: Run tests on multiple platforms in CI (Linux, macOS, Windows)
