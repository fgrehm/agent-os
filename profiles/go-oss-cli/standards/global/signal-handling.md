## Signal handling and graceful shutdown

- **Context usage**: Use `context.Context` throughout for cancellation propagation
- **Signal handling**: Listen for SIGTERM and SIGINT for graceful shutdown
- **Graceful shutdown**: Complete in-flight operations when possible before exiting
- **Cleanup on exit**: Use defer or shutdown handlers to clean up resources (files, connections)
- **Timeout for shutdown**: Set reasonable timeout (e.g., 30s) for graceful shutdown before force exit
- **Cancel contexts**: Cancel context on signal to propagate shutdown to all goroutines
- **Exit codes**: Return appropriate exit codes (0 for clean shutdown, 130 for SIGINT, 143 for SIGTERM)
- **Log shutdown**: Log when graceful shutdown is initiated and completed
- **Avoid data loss**: Flush buffers, close files properly, commit transactions before exit
- **Multiple signals**: Handle second signal as force quit (don't wait for graceful shutdown)
- **Long operations**: Check context cancellation in loops and long-running operations
- **Signal package**: Use `os/signal` package and `signal.NotifyContext()` for clean signal handling
