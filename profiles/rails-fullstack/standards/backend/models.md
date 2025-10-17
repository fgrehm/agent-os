# Database Models

For comprehensive model guidelines, refer to:

- **Model Standards**: @docs/claude/models.md - **CRITICAL** for adding columns or creating models
- **Development Guidelines**: @docs/claude/development-guidelines.md

## Key Points

- **PII Encryption**: All PII fields MUST be encrypted using `encrypts` with deterministic mode
- **UUID Primary Keys**: All tables use UUID primary keys, never expose in URLs
- **Soft Deletion**: Use `deleted_at` timestamps, never hard delete records
- **Data Integrity**: Use database constraints (NOT NULL, UNIQUE, foreign keys) at database level
- **Audit Trail**: PaperTrail enabled for change tracking on sensitive models
