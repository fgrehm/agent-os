## Go code style and conventions

- **Follow Go idioms**: Write idiomatic Go code that follows community conventions
- **gofmt formatting**: All code must be formatted with `gofmt` (or `goimports`)
- **Error handling**: Always check and handle errors explicitly; never ignore errors
- **Variable naming**: Use short, concise names in small scopes (`i`, `err`), descriptive names in larger scopes
- **Exported names**: Start with uppercase for exported functions/types, lowercase for unexported
- **Interface naming**: Single-method interfaces end in `-er` (e.g., `Reader`, `Writer`)
- **Package comments**: Every package should have a package-level comment before the package declaration
- **Function comments**: Exported functions must have comments starting with the function name
- **Early returns**: Prefer early returns and guard clauses over nested conditionals
- **Context usage**: Accept `context.Context` as first parameter for functions that perform I/O or long-running operations
- **Pointer receivers**: Use pointer receivers for methods that mutate state or for large structs
- **Zero values**: Design types to have useful zero values when possible
