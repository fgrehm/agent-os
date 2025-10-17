# Testing Standards

For comprehensive testing guidelines, refer to:

- **Testing Standards**: @docs/claude/testing.md
- **Factories & Fixtures**: @docs/claude/factories-and-fixtures.md

## Testing Framework

- **Ruby/Rails**: RSpec with FactoryBot for fixtures
- **JavaScript**: Vitest with Happy DOM for component testing
- **Coverage Requirements**: 70% minimum for JS (branches, functions, lines, statements)

## Best Practices

- **Write Minimal Tests During Development**: Do NOT write tests for every change or intermediate step. Focus on completing the feature implementation first, then add strategic tests only at logical completion points
- **Test Only Core User Flows**: Write tests exclusively for critical paths and primary user workflows
- **Defer Edge Case Testing**: Do NOT test edge cases, error states, or validation logic unless they are business-critical
- **Test Behavior, Not Implementation**: Focus on what the code does, not how it does it
- **Clear Test Names**: Use descriptive names that explain what's being tested and the expected outcome
- **Avoid let/let!**: Prefer explicit setup in RSpec tests (new code only)
- **Use instance_double**: Prefer `instance_double` over generic `double` for type safety
