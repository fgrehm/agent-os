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

### Infer Base Directory from Script Location

**Current behavior:** All scripts hardcode `BASE_DIR="$HOME/agent-os"` (now with `${BASE_DIR:-$HOME/agent-os}` for environment override).

**Proposed improvement:** Infer base directory from script location instead:

```bash
# Instead of hardcoding:
BASE_DIR="${BASE_DIR:-$HOME/agent-os}"

# Infer from script location:
SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
BASE_DIR="${BASE_DIR:-$(dirname "$SCRIPT_DIR")}"
# If script is in ~/agent-os/scripts/foo.sh, BASE_DIR becomes ~/agent-os
```

**Benefits:**
- Works with custom installation paths without flags
- More flexible for development/testing (can clone repo anywhere)
- Eliminates hardcoded home directory assumption
- Still supports `BASE_DIR` environment override
- `--base-dir` flag can override for cross-directory operations

**Implementation:**
- Update all scripts: `base-install.sh`, `project-install.sh`, `project-update.sh`, `create-profile.sh`, `create-role.sh`
- Test with different installation locations
- Document behavior in script help text

---

### Preserve Project Data During Re-install

**Problem:** `--re-install` flag completely deletes `agent-os/` folder, including `product/` and `specs/` directories that contain valuable project data and specifications.

**Proposed solution:** Add option to preserve project data during re-installation:

```bash
~/agent-os/scripts/project-install.sh \
  --profile rails-fullstack \
  --re-install \
  --preserve-data  # or --preserve product,specs
```

**What should be preserved:**
- `agent-os/product/` - Product vision, roadmap, tech-stack
- `agent-os/specs/` - All feature specifications and implementation history
- Potentially `agent-os/config.yml` settings (profile, modes, tools)

**What should be reinstalled:**
- `agent-os/standards/` - Refreshed from profile
- `agent-os/roles/` - Refreshed from profile
- `agent-os/commands/` (if in both modes) - Refreshed from profile
- `.claude/agents/agent-os/` - Regenerated from templates
- `.claude/commands/agent-os/` - Refreshed from profile

**Implementation approach:**
1. Add `--preserve-data` flag (or `--preserve <comma-separated-dirs>`)
2. Before deletion, move preserved directories to temp location
3. Reinstall from profile
4. Restore preserved directories
5. Update config.yml with new profile/settings

**Alternative approach:** Backup suggestion instead of hard preserve:
```
⚠️  Re-install will delete agent-os/ including product/ and specs/

   Would you like to:
   1. Continue and delete everything
   2. Backup to agent-os.backup/ and continue
   3. Cancel
```

**Benefits:**
- Safer profile switching (keeps project context)
- Allows refreshing standards/roles without losing specs
- More user-friendly for experimentation

**Considerations:**
- Should `config.yml` be preserved or regenerated?
- How to handle profile-specific files in preserved directories?
- Document the preserved vs refreshed behavior clearly

---

### Other Ideas

- Profile templates/examples repository
- Mixins system (per discussion #200)
- `/remember` command for capturing standards mid-development
- Profile validation command
