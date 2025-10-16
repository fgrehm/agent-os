# Navigator Profile

Agent OS profile with pause-for-review workflow. Like a navigator guiding a pilot through checkpoints, this profile pauses at natural milestones for your review and approval before proceeding.

## Overview

This profile inherits from the `default` profile and overrides specific commands to add **pause-for-review checkpoints** throughout the spec-driven development workflow. The orchestrator pauses after each significant milestone, allowing you to review changes, test functionality, and create incremental commits before proceeding.

## Features

### Pause-for-Review Workflow

All commands now pause at natural checkpoints:

- **`/plan-product`**: Pauses after creating product planning documents
- **`/new-spec`**: Pauses after requirements gathering and research
- **`/create-spec`**: Pauses after spec and tasks creation
- **`/implement-spec`**: Pauses after EACH task group (prevents massive changesets)

### What Happens at Each Pause

1. **Clear summary** of what was accomplished
2. **List of files** created/modified/deleted
3. **Commit strategy suggestion** (single commit vs multiple)
4. **Wait for explicit approval** before proceeding
5. **Orchestrator creates commits** after user approval
6. **Confirmation** before moving to next phase

### Benefits

- ✅ **Incremental commits** - Clean, meaningful git history
- ✅ **Review opportunities** - Catch issues early
- ✅ **Test between phases** - Verify functionality incrementally
- ✅ **Course correction** - Adjust direction before compounding issues
- ✅ **User control** - You decide when to proceed

## Installation

### On New Projects

```bash
~/agent-os/scripts/project-install.sh --profile navigator
```

### Set as Default

Edit `~/agent-os/config.yml`:

```yaml
default_profile: navigator
```

Then all new projects will use this profile automatically.

### On Existing Projects

Re-run the project installer:

```bash
cd /path/to/your/project
~/agent-os/scripts/project-install.sh --profile navigator
```

## What's Overridden

This profile overrides only the files necessary for pause-for-review functionality:

### Commands (9 files)
- `commands/implement-spec/multi-agent/implement-spec.md`
- `commands/implement-spec/single-agent/1-implement-spec.md`
- `commands/implement-spec/single-agent/2-verify-implementation.md`
- `commands/create-spec/multi-agent/create-spec.md`
- `commands/create-spec/single-agent/3-verify-spec.md`
- `commands/new-spec/multi-agent/new-spec.md`
- `commands/new-spec/single-agent/2-research-spec.md`
- `commands/plan-product/multi-agent/plan-product.md`
- `commands/plan-product/single-agent/4-create-tech-stack.md`

### Agent Templates (2 files)
- `agents/templates/implementer.md` - Adds "Return Control for Review" step
- `agents/templates/verifier.md` - Adds "Return Control for Review" step

### Standards (1 file)
- `standards/global/agent-workflow.md` - Complete pause-for-review guidance

### Workflows (2 files)
- `workflows/implementation/implementer-responsibilities.md`
- `workflows/implementation/verifier-responsibilities.md`

**Everything else** (agents, roles, other standards, other workflows) is inherited from the `default` profile.

## Background

This profile addresses the "massive commits problem" by introducing natural pause points where the orchestrator stops, summarizes work, and waits for user approval before creating commits and proceeding to the next phase.

## Maintenance

This profile can be version-controlled separately and updated independently of the base Agent OS installation. When upstream `default` profile is updated, this profile automatically inherits those changes while maintaining its pause-for-review enhancements.

To update to latest upstream changes:
```bash
cd ~/agent-os
git pull origin main
```

Your `navigator` profile will continue to override only the specific files it provides.

## Example Workflow

```bash
# 1. Plan your product (one-time setup)
/plan-product
# → Pauses after creating mission.md, roadmap.md, tech-stack.md
# → You review, approve commit, continue

# 2. Start a new feature spec
/new-spec
# → Pauses after requirements gathering
# → You review, approve commit, continue

# 3. Create the detailed spec
/create-spec
# → Pauses after spec.md and tasks.md creation
# → You review, approve commit, continue

# 4. Implement the spec (task by task)
/implement-spec
# → Pauses after Task Group 1
# → You review, test, approve commit
# → Pauses after Task Group 2
# → You review, test, approve commit
# → ... continues for each task group
# → Pauses before verification
# → You review all implementation commits, approve
```

## License

Same as Agent OS (MIT)
