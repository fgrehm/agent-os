# Database Migrations

For comprehensive migration guidelines, refer to:

- **Development Guidelines**: @docs/claude/development-guidelines.md
- **Model Standards**: @docs/claude/models.md

## Migration Best Practices

- **Reversible Migrations**: Always implement rollback/down methods to enable safe migration reversals
- **Small, Focused Changes**: Keep each migration focused on a single logical change
- **Bulk Changes**: Use `change_table bulk: true` for multiple column changes
- **PII Encryption**: Use `encrypts` with deterministic mode for all PII fields
- **Never Modify Deployed Migrations**: Once deployed, create a new migration instead
