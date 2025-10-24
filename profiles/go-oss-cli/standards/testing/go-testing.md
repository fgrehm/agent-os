## Go testing best practices

- **Test file naming**: Name test files `*_test.go` in the same package
- **Table-driven tests**: Use table-driven tests for testing multiple scenarios
- **Test function naming**: Name tests `TestFunctionName_Scenario` for clarity
- **Test helpers**: Use `t.Helper()` in helper functions to get correct line numbers in failures
- **Subtests**: Use `t.Run()` for subtests within table-driven tests
- **Test coverage**: Aim for meaningful coverage of critical paths (use `go test -cover`)
- **Failing fast**: Use `t.Fatal()` for setup failures, `t.Error()` for test failures
- **Test fixtures**: Place test data in `testdata/` directory
- **Mocking**: Use interfaces for dependencies to enable easy mocking
- **Example tests**: Use `Example` functions for documentation and testing examples
- **Benchmark tests**: Add benchmark tests (`BenchmarkXxx`) for performance-critical code
