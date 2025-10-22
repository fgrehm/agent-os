## Validation and Testing Standards

### Overview

Thorough validation ensures commands, agents, and skills work correctly before deployment. This standard covers testing strategies, validation processes, and quality assurance for Claude Code extensions.

### Validation Layers

**Three Levels of Validation:**

1. **Syntax Validation**: Is the file structure correct?
2. **Functional Validation**: Does it accomplish its purpose?
3. **Integration Validation**: Does it work with other components?

### Syntax Validation

**Markdown Linting:**

```bash
# Lint all Claude Code files
markdownlint .claude/**/*.md

# Specific patterns
markdownlint .claude/commands/**/*.md
markdownlint .claude/agents/**/*.md
markdownlint .claude/skills/**/*.md
```

**Common Issues:**
- Inconsistent heading levels
- Mixed list markers
- Missing blank lines
- Trailing whitespace
- No language in code blocks

**YAML Frontmatter Validation:**

```bash
# Extract and validate frontmatter
yamllint .claude/**/*.md

# Or use yq for parsing
yq eval '.name' .claude/agents/test-writer.md
```

**Common Issues:**
- Invalid YAML syntax
- Missing required fields
- Incorrect field names
- Wrong value types
- Inconsistent formatting

**Automated Checks:**

```bash
#!/bin/bash
# validate-claude-code.sh

echo "Validating Markdown..."
markdownlint .claude/**/*.md || exit 1

echo "Validating YAML frontmatter..."
for file in .claude/**/*.md; do
  echo "Checking: $file"
  # Extract frontmatter and validate
  sed -n '/^---$/,/^---$/p' "$file" | yamllint - || exit 1
done

echo "Validation passed!"
```

### Functional Validation

**Command Testing:**

**Manual Test Process:**

1. **Invoke Command**
   ```
   /command-name test-arg
   ```

2. **Verify Behavior**
   - Does it appear in palette?
   - Description clear and accurate?
   - Argument hint helpful?
   - Executes without errors?

3. **Test Edge Cases**
   - Invalid arguments
   - Missing files
   - No arguments (if optional)
   - Special characters in args

4. **Verify Output**
   - Correct result produced?
   - Error messages clear?
   - Files created/modified as expected?

**Test Checklist:**

```markdown
## Command: /add-test

- [ ] Appears in command palette
- [ ] Description: "Add test file for a given source file"
- [ ] Argument hint: "<source-file>"
- [ ] Invokes successfully with valid file
- [ ] Creates test file at correct location
- [ ] Test file has correct content
- [ ] Handles missing file gracefully
- [ ] Error messages are clear
- [ ] Tool restrictions work (only Read, Write allowed)
- [ ] Works with TypeScript files
- [ ] Works with JavaScript files
- [ ] Works with Python files
```

**Agent Testing:**

**Manual Test Process:**

1. **Invoke Agent via Task Tool**
   ```markdown
   I'm going to use the Task tool to invoke the test-writer agent.
   ```

2. **Verify Agent Execution**
   - Agent available in subagent list?
   - Description triggers appropriate selection?
   - Tools accessible as configured?
   - Returns expected results?

3. **Test Scenarios**
   - Happy path (valid input)
   - Error cases (invalid input)
   - Edge cases (empty file, large file)
   - Tool restrictions (can't use forbidden tools)

4. **Verify Output**
   - Result format matches specification?
   - Clear summary provided?
   - Issues reported appropriately?
   - Recommendations useful?

**Test Checklist:**

```markdown
## Agent: test-writer

- [ ] Appears in available subagents
- [ ] Description: "Generates comprehensive test files..."
- [ ] Invokes successfully via Task tool
- [ ] Can use Read tool
- [ ] Can use Write tool
- [ ] Can use Glob tool
- [ ] Cannot use Bash (not in tools list)
- [ ] Generates correct test file
- [ ] Follows naming convention
- [ ] Uses detected test framework
- [ ] Returns structured report
- [ ] Handles missing source file
- [ ] Reports errors clearly
- [ ] Model selection works (sonnet)
- [ ] Color displays correctly (green)
```

**Skill Testing:**

**Manual Test Process:**

1. **Trigger Skill Activation**
   - Use trigger words from description
   - Example: "Extract tables from this PDF"

2. **Verify Activation**
   - Claude selects skill automatically?
   - Skill description was clear enough?
   - Right skill for the task?

3. **Test Functionality**
   - Skill accomplishes purpose?
   - Uses only allowed tools?
   - Handles errors gracefully?
   - Returns useful results?

4. **Test Discovery**
   - Different trigger words work?
   - Activates for right scenarios?
   - Doesn't activate for wrong scenarios?

**Test Checklist:**

```markdown
## Skill: pdf-processor

- [ ] Activates when user mentions "PDF"
- [ ] Activates for "extract tables"
- [ ] Activates for "process document"
- [ ] Does NOT activate for generic "process file"
- [ ] Can use Read tool
- [ ] Can use Write tool
- [ ] Can use Bash tool
- [ ] Cannot use Edit (not in allowed-tools)
- [ ] Extracts text correctly
- [ ] Extracts tables correctly
- [ ] Handles missing file
- [ ] Handles invalid PDF
- [ ] Error messages clear
- [ ] Reports tool requirements
- [ ] Instructions in SKILL.md work
- [ ] Progressive disclosure effective (links to reference.md)
```

### Integration Validation

**Multi-Component Testing:**

**Command → Agent:**

```markdown
Test: /orchestrate-tasks command spawns multiple agents

1. Invoke /orchestrate-tasks
2. Verify command spawns test-writer agent
3. Verify command spawns doc-generator agent
4. Verify agents complete independently
5. Verify results aggregated correctly
```

**Agent → Skill:**

```markdown
Test: Agent uses skill during execution

1. Invoke agent that processes PDFs
2. Verify agent activates pdf-processor skill
3. Verify skill executes correctly
4. Verify agent receives skill results
5. Verify agent incorporates results in output
```

**Workflow Integration:**

```markdown
Test: Complete workflow end-to-end

1. User invokes /add-feature
2. Command spawns spec-writer agent
3. Spec-writer uses requirements skill
4. Spec-writer creates spec.md
5. Command spawns implementer agent
6. Implementer reads spec.md
7. Implementer creates implementation
8. Command spawns test-writer agent
9. Test-writer creates tests
10. All results aggregated and reported
```

### Error Handling Validation

**Required Error Cases:**

**File Not Found:**
```markdown
Test: Command with missing file

Input: /add-test src/missing.ts
Expected:
```
ERROR: Source file not found: src/missing.ts
Suggestion: Check file path and try again
```
Status: Command stops, no test file created
```

**Invalid Input:**
```markdown
Test: Command with invalid argument

Input: /add-test
Expected:
```
ERROR: Missing required argument: <source-file>
Usage: /add-test <source-file>
```
Status: Command stops with usage help
```

**Tool Restriction:**
```markdown
Test: Agent tries to use forbidden tool

Setup: Agent configured with tools: Read, Write
Action: Agent attempts to use Bash tool
Expected: Tool call blocked, error reported
Status: Agent should handle gracefully
```

**Dependency Missing:**
```markdown
Test: Skill requires missing tool

Setup: pdf-processor requires poppler-utils
Action: User requests PDF processing
Expected:
```
ERROR: Required tool not found: pdftotext
Install: apt-get install poppler-utils
```
Status: Skill reports clearly, suggests fix
```

### Performance Validation

**Token Efficiency:**

```markdown
Test: Measure prompt token usage

1. Count tokens in command/agent/skill prompt
2. Verify under reasonable limit:
   - Commands: < 2,000 tokens
   - Agents: < 3,000 tokens
   - Skills: < 5,000 tokens (SKILL.md only)

Tools:
- Claude token counter
- tiktoken library
- Manual estimation (4 chars ≈ 1 token)
```

**Execution Speed:**

```markdown
Test: Measure execution time

1. Time command/agent execution
2. Verify reasonable performance:
   - Simple commands: < 10s
   - Complex workflows: < 60s
   - Agent invocation overhead: < 5s

Note: Actual times vary by model and task complexity
```

**Resource Usage:**

```markdown
Test: Check file operations

1. Monitor file reads/writes
2. Verify minimal operations:
   - Read files only when needed
   - Write files only when necessary
   - No redundant operations
   - Efficient glob/grep patterns
```

### Cross-Model Validation

**Test Across Models:**

```markdown
Test: Agent with different models

Setup: Agent with model: inherit
Test with:
1. Parent using Haiku
   - Verify basic functionality
   - May need more explicit instructions

2. Parent using Sonnet
   - Verify full functionality
   - Should handle complexity well

3. Parent using Opus
   - Verify advanced scenarios
   - Should excel at complex reasoning

Note: Commands and skills use parent model
```

**Model-Specific Issues:**

```markdown
Haiku:
- May need more explicit instructions
- Better with structured, clear prompts
- Test simple and complex scenarios

Sonnet:
- Balanced performance
- Good for most use cases
- Test standard workflows

Opus:
- Handles complexity and nuance
- Good for ambiguous scenarios
- Test edge cases and complex reasoning
```

### Regression Testing

**Maintain Test Suite:**

```markdown
tests/
├── commands/
│   ├── test-add-test.md       # Test cases for /add-test
│   └── test-format-code.md    # Test cases for /format-code
├── agents/
│   ├── test-code-reviewer.md  # Test cases for code-reviewer
│   └── test-test-writer.md    # Test cases for test-writer
└── skills/
    └── test-pdf-processor.md   # Test cases for pdf-processor
```

**Test Case Format:**

```markdown
# Test: /add-test Command

## Test Case 1: TypeScript File

Input:
- Command: /add-test src/utils.ts
- File exists: Yes
- Contains: `export function add(a: number, b: number)`

Expected:
- Creates: src/utils.test.ts
- Contains: import statement
- Contains: describe block
- Contains: test for add function
- Uses Jest syntax

Status: ✓ Pass

## Test Case 2: Missing File

Input:
- Command: /add-test src/missing.ts
- File exists: No

Expected:
- Error: "Source file not found"
- No test file created
- Suggests checking path

Status: ✓ Pass

## Test Case 3: No Argument

Input:
- Command: /add-test

Expected:
- Error: "Missing required argument"
- Shows usage: /add-test <source-file>

Status: ✓ Pass
```

**Run Regression Tests:**

```bash
# Before releasing changes
./scripts/run-tests.sh

# After modifying commands
./scripts/test-commands.sh

# After updating agents
./scripts/test-agents.sh
```

### Continuous Validation

**Pre-commit Hooks:**

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running Claude Code validation..."

# Lint markdown
markdownlint .claude/**/*.md || {
  echo "Markdown linting failed"
  exit 1
}

# Validate frontmatter
./scripts/validate-frontmatter.sh || {
  echo "Frontmatter validation failed"
  exit 1
}

# Check for common issues
./scripts/check-conventions.sh || {
  echo "Convention check failed"
  exit 1
}

echo "Validation passed!"
```

**CI/CD Pipeline:**

```yaml
# .github/workflows/validate-claude-code.yml
name: Validate Claude Code Extensions

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Lint Markdown
        run: markdownlint .claude/**/*.md

      - name: Validate YAML
        run: yamllint .claude/**/*.md

      - name: Run Tests
        run: ./scripts/run-tests.sh

      - name: Check Conventions
        run: ./scripts/check-conventions.sh
```

### Validation Checklist

**Before Committing:**

```markdown
## Syntax

- [ ] Markdown lints without errors
- [ ] YAML frontmatter is valid
- [ ] No trailing whitespace
- [ ] Consistent formatting
- [ ] Code blocks have language specified

## Functionality

- [ ] Command/agent/skill invokes successfully
- [ ] Accomplishes stated purpose
- [ ] Handles valid input correctly
- [ ] Handles invalid input gracefully
- [ ] Error messages are clear

## Documentation

- [ ] Description is clear and accurate
- [ ] Examples work as documented
- [ ] Prerequisites are listed
- [ ] Error cases documented
- [ ] Cross-references are valid

## Integration

- [ ] Works with related components
- [ ] Tool restrictions respected
- [ ] Model selection appropriate
- [ ] Returns expected format

## Performance

- [ ] Token usage reasonable
- [ ] Execution time acceptable
- [ ] No redundant operations

## Testing

- [ ] Test cases created
- [ ] Edge cases covered
- [ ] Regression tests pass
- [ ] Documented in test suite
```

### Validation Tools

**Recommended Tools:**

```bash
# Markdown linting
npm install -g markdownlint-cli

# YAML validation
pip install yamllint

# Token counting
pip install tiktoken

# JSON validation (for settings.json)
jq . .claude/settings.json
```

**Custom Validation Scripts:**

```bash
#!/bin/bash
# scripts/validate-frontmatter.sh

for file in .claude/agents/*.md; do
  echo "Validating: $file"

  # Extract frontmatter
  frontmatter=$(sed -n '/^---$/,/^---$/p' "$file")

  # Check required fields
  echo "$frontmatter" | grep -q "^name:" || {
    echo "ERROR: Missing 'name' field in $file"
    exit 1
  }

  echo "$frontmatter" | grep -q "^description:" || {
    echo "ERROR: Missing 'description' field in $file"
    exit 1
  }

  echo "$frontmatter" | grep -q "^tools:" || {
    echo "ERROR: Missing 'tools' field in $file"
    exit 1
  }
done

echo "Frontmatter validation passed!"
```

### Common Validation Issues

**Issue: Command doesn't appear in palette**
- Check file location: `.claude/commands/`
- Verify filename extension: `.md`
- Check frontmatter required field: `description`
- Restart Claude Code if needed

**Issue: Agent not available for Task tool**
- Check file location: `.claude/agents/`
- Verify `name` field matches filename
- Check `tools` field is present
- Verify YAML syntax

**Issue: Skill doesn't activate**
- Review `description` field
- Add "when to use" triggers
- Include relevant keywords
- Test with different phrasings

**Issue: Tool access denied**
- Check `allowed-tools` (commands/skills)
- Check `tools` (agents)
- Verify tool names match exactly
- Check settings.json permissions

### Related Standards

- `@agent-os/standards/global/coding-style.md` - Formatting standards
- `@agent-os/standards/global/conventions.md` - File organization
- `@agent-os/standards/claude-code/command-design.md` - Command patterns
- `@agent-os/standards/claude-code/agent-design.md` - Agent patterns
- `@agent-os/standards/claude-code/skill-design.md` - Skill testing

### References

- [markdownlint](https://github.com/DavidAnson/markdownlint)
- [yamllint](https://yamllint.readthedocs.io/)
- [Claude Code Testing Guide](https://docs.claude.com/en/docs/claude-code/testing)
