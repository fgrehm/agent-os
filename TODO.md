# TODO - Upstream Contributions

This file tracks improvements to contribute back to `buildermethods/agent-os`.

## High Priority

### Add Non-Interactive Flags to Scripts

**Problem:** `create-profile.sh` and `create-role.sh` are fully interactive, making them difficult to use programmatically from commands/agents.

**Proposed Solution:** Add non-interactive mode with flags:

**For `create-profile.sh`:**
```bash
~/agent-os/scripts/create-profile.sh \
  --name rails-api \
  --inherit-from default \
  --non-interactive
```

**For `create-role.sh`:**
```bash
~/agent-os/scripts/create-role.sh \
  --profile rails-api \
  --type implementer \
  --id database-engineer \
  --description "Handles migrations, models, schemas" \
  --role-text "You are a database engineer..." \
  --tools "Write, Read, Bash, WebFetch" \
  --model inherit \
  --color orange \
  --responsibilities "Create migrations,Create models" \
  --out-of-scope "API endpoints,UI components" \
  --standards "global/*,backend/*,testing/*" \
  --verified-by "backend-verifier" \
  --non-interactive
```

**Benefits:**
- Enables programmatic use from bootstrap commands/agents
- Scripts handle structure/validation/permissions
- Maintains single source of truth for file generation
- Backward compatible (keep interactive mode as default)

**Implementation Notes:**
- Use `getopts` for flag parsing
- Keep all existing validation logic
- Add `--help` flag with examples
- Test on Linux, macOS, Windows (Git Bash/WSL)

**Related to:** Bootstrap commands in this fork currently write files directly to work around interactive limitation.

---

## Future Ideas

### Improve Flag Handling for Array Values

**Current limitation:** Comma-separated lists for `--responsibilities`, `--out-of-scope`, `--standards`, etc. can be problematic with complex values containing commas or special characters.

**Proposed improvement:** Support repeating flags for array values:

```bash
~/agent-os/scripts/create-role.sh \
  --profile rails-api \
  --type implementer \
  --id database-engineer \
  --description "Handles migrations, models, schemas" \
  --role-text "You are a database engineer..." \
  --color orange \
  --responsibilities "Create and manage database migrations" \
  --responsibilities "Define ActiveRecord models and associations" \
  --responsibilities "Write efficient SQL queries and optimize database performance" \
  --out-of-scope "API endpoints and controllers" \
  --out-of-scope "Frontend UI components" \
  --standards "global/*" \
  --standards "backend/*" \
  --standards "testing/*" \
  --verified-by "backend-verifier" \
  --non-interactive
```

**Benefits:**
- No escaping issues with commas in values
- More readable in scripts/documentation
- Follows common CLI patterns (like `--option value --option value2`)
- Can still support comma-separated as shorthand

**Implementation approach:**
- Append to arrays instead of replacing: `ROLE_AREAS+=("$2")`
- Support both styles: `--responsibilities "a,b,c"` OR `--responsibilities "a" --responsibilities "b" --responsibilities "c"`
- Document both approaches in help text

---

### Other Ideas

- Profile templates/examples repository
- Mixins system (per discussion #200)
- `/remember` command for capturing standards mid-development
- Profile validation command
