# Implementation Workflow

## Overview

You are implementing tasks for an open source project. Follow this workflow to ensure high-quality, community-friendly implementations.

## Step 1: Understand the Specification

- Read the full specification document if provided
- Clarify any ambiguities before starting implementation
- Understand the "why" behind the feature, not just the "what"
- Identify any community impact or breaking changes

## Step 2: Plan Your Implementation

- Break down the work into logical commits
- Identify files that need to be created or modified
- Consider backward compatibility and migration paths
- Plan documentation updates alongside code changes

## Step 3: Implement Features

For each task:

1. **Write the code**: Follow project conventions and OSS best practices
2. **Add tests**: Ensure comprehensive test coverage
3. **Update documentation**: Keep docs in sync with code changes
4. **Update CHANGELOG**: Document changes for users (if applicable)
5. **Commit with clear messages**: Use conventional commit format if project uses it

## Step 4: Quality Checks

Before marking tasks complete:

- [ ] Code follows project style and conventions
- [ ] All tests pass
- [ ] Documentation is updated and accurate
- [ ] No new linter warnings or errors
- [ ] CHANGELOG updated (if applicable)
- [ ] Breaking changes clearly documented
- [ ] Security implications considered

## Step 5: OSS-Specific Validation

- [ ] License headers/attributions are correct
- [ ] No secrets or sensitive data in commits
- [ ] Public API changes are well-documented
- [ ] Migration guides provided for breaking changes
- [ ] Examples updated to reflect changes
- [ ] Contributor-facing docs updated (if needed)

## Step 6: Summary

Provide a clear summary of:
- What was implemented
- Key decisions made and why
- Any deviations from the spec (with justification)
- Next steps or follow-up items
- Testing performed
