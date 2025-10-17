# API & Services

For comprehensive service and API guidelines, refer to:

- **Services & Patterns**: @docs/claude/services-and-patterns.md
- **Development Guidelines**: @docs/claude/development-guidelines.md

## Service Pattern

- **Monadic Results**: All services use `Success()` and `Failure()` returns
- **Block Handling**: Handle service results with `do |on|` blocks, not direct variable assignment
- **Type Declarations**: Use `Type::` module for argument type declarations
- **No StandardError Rescue**: Use `Failure()` for expected failures only

## Controllers

- **RESTful Organization**: Namespace controllers (Auth, Admin, Customers)
- **UUID Parameters**: Use `uuid` not `id` in routes and controllers
- **Service Delegation**: Controllers delegate to service objects for business logic
