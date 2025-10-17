# Database Queries

For comprehensive query guidelines, refer to:

- **Development Guidelines**: @docs/claude/development-guidelines.md

## Query Best Practices

- **Prevent SQL Injection**: Always use parameterized queries or ActiveRecord methods
- **Avoid N+1 Queries**: Use eager loading (`includes`, `joins`) to fetch related data
- **Index Strategic Columns**: Index columns used in WHERE, JOIN, and ORDER BY clauses
- **Use Transactions**: Wrap related database operations in transactions for data consistency
- **UUID Primary Keys**: Never expose UUIDs in URLs, use separate slug/identifier fields
