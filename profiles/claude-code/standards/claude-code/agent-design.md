## Claude Code Agents design standards

### Overview

Claude Code Agents are specialized sub-agents that can be invoked by the main Claude instance to handle specific tasks. Agents run independently with their own context, tools, and model configuration.

**Key Concepts:**
- **Main Claude**: The primary Claude instance the user interacts with
- **Subagent**: A specialized agent invoked via the Task tool
- **Agent Definition**: Markdown file with YAML frontmatter defining agent capabilities

### File Structure

**Location:** `.claude/agents/agent-name.md`

**Single-file agents:**
```
.claude/agents/
├── code-reviewer.md
├── test-writer.md
└── doc-generator.md
```

### Agent Frontmatter

**Required Structure:**
```yaml
---
name: agent-name
description: What the agent does and its role
tools: Tool1, Tool2, Tool3
color: blue          # Optional
model: haiku         # Optional
---
```

**Field Descriptions:**

**name** (Required):
- kebab-case naming: `link-processor`, `spec-writer`, `code-reviewer`
- Descriptive of agent's role
- Used to invoke agent: `Task(subagent_type="agent-name")`
- Should match filename without .md extension

**description** (Required):
- Clear statement of agent's purpose and responsibilities
- Written in third person: "Processes links" not "I process links"
- 1-2 sentences max
- Used for subagent discovery by main Claude
- Should include WHAT it does and WHEN to use it

**tools** (Required):
- Comma-separated list of tools agent can use
- Be restrictive: only include necessary tools
- Common tools: Read, Write, Edit, Bash, WebFetch, Task, Glob, Grep
- Include Task if agent needs to spawn sub-agents
- Order doesn't matter but alphabetical is conventional

**color** (Optional):
- Visual identifier in Claude Code UI
- Options: `red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, `gray`
- Use consistently across related agents
- Default: `blue` if not specified
- Semantic usage:
  - `red`: Critical/important operations
  - `blue`: General purpose
  - `green`: Success/validation
  - `yellow`: Warning/review
  - `purple`: Specialized/analysis
  - `gray`: Utility/helper

**model** (Optional):
- Which Claude model to use: `haiku`, `sonnet`, `opus`
- `inherit`: Use same model as parent (default)
- Selection guide:
  - `haiku`: Fast, cost-effective for simple, repetitive tasks
  - `sonnet`: Balanced capability and cost (most common)
  - `opus`: Complex reasoning, critical accuracy needs
- Default: `inherit` from parent context

### Agent Design Principles

**The Specialization Principle:**
> Each agent should have a narrow, well-defined role.

**Good Agent Roles:**
- Code reviewer for specific language
- Test file generator
- Documentation writer
- Link processor
- Spec validator

**Bad Agent Roles (too broad):**
- General helper
- Does everything
- Smart assistant
- Problem solver

**Why Specialization Matters:**

1. **Clear Responsibility**: Easy to know when to invoke
2. **Focused Context**: Agent prompt can be highly specific
3. **Tool Minimalism**: Fewer tools = less confusion
4. **Testable Behavior**: Narrow scope = easier to validate
5. **Reusability**: Specialized agents compose well

### Tool Selection

**Principle:** Grant minimum necessary tools.

**Read-Only Analysis:**
```yaml
tools: Read, Grep, Glob
```
Use for: Code analysis, documentation review, pattern detection

**File Processing:**
```yaml
tools: Read, Write, Edit
```
Use for: Code generation, refactoring, file transformations

**Development Workflow:**
```yaml
tools: Bash, Read, Write, Edit
```
Use for: Running tests, build processes, git operations

**Web Integration:**
```yaml
tools: WebFetch, Read, Write
```
Use for: API calls, web scraping, external data fetching

**Multi-Agent Orchestration:**
```yaml
tools: Task, Read, Write
```
Use for: Coordinating multiple specialized sub-agents

**Tool Restriction Benefits:**

1. **Security**: Limits potential damage from errors
2. **Focus**: Fewer options = clearer path
3. **Debugging**: Easier to trace what went wrong
4. **Intentionality**: Explicit about capabilities

### Invoking Agents

**From Main Claude:**

```markdown
I'm going to use the Task tool to invoke the code-reviewer agent
to analyze this code for issues.
```

**Task Tool Usage:**
```python
Task(
    subagent_type="code-reviewer",
    description="Review code for issues",
    prompt="Review src/utils.ts for type safety issues and suggest improvements"
)
```

**Agent Selection:**

Main Claude decides which agent to invoke based on:
- Agent descriptions in `.claude/agents/`
- Context of current task
- Availability of specialized agents

### Agent Prompt Structure

**Effective Agent Prompts:**

```markdown
---
name: test-writer
description: Generates comprehensive test files for source files using project testing conventions
tools: Read, Write, Glob
color: green
model: sonnet
---

# Test Writer Agent

You are a testing specialist focused on writing comprehensive, maintainable tests.

## Your Role

Generate test files for given source files following project conventions:
- Detect test framework from project (Jest, Vitest, pytest, etc.)
- Create appropriate test structure
- Add meaningful test cases covering main functionality
- Follow project naming conventions

## Process

1. Read the source file
2. Identify exported functions/classes
3. Detect test framework via package.json or project structure
4. Create test file with:
   - Proper imports
   - Describe/it blocks (or equivalent)
   - Test cases for main functionality
   - Mock setup if needed

## Guidelines

- Use project's existing test patterns
- Write clear test descriptions
- Cover happy path and basic edge cases
- Don't over-test trivial code
- Follow naming convention: [filename].test.[ext]

## Examples

For `src/utils/format.ts` → `src/utils/format.test.ts`
For `lib/parser.py` → `tests/test_parser.py`
```

**Key Elements:**

1. **Role Definition**: Clear identity and expertise
2. **Responsibilities**: What the agent is supposed to do
3. **Process**: Step-by-step approach
4. **Guidelines**: Rules and best practices
5. **Examples**: Concrete illustrations

### Model Selection Strategy

**Haiku** (fast, economical):
- Repetitive tasks (formatting, boilerplate)
- Simple transformations
- Pattern-based work
- When speed matters more than nuance

**Sonnet** (balanced):
- Most general-purpose agent work
- Code review
- Test generation
- Documentation writing
- Default choice for most agents

**Opus** (powerful, expensive):
- Complex architectural decisions
- Critical security reviews
- Novel problem solving
- When accuracy is paramount

**Inherit** (recommended default):
- Use parent's model
- Maintains consistency
- User controls cost via main session
- Simplifies configuration

### Agent Communication Patterns

**Parent → Agent:**
- Detailed task description in Task tool prompt
- Context about what to analyze/generate
- Specific requirements or constraints

**Agent → Parent:**
- Agent returns single message with results
- Include summary of what was done
- Highlight issues or recommendations
- Provide artifacts (code, analysis, etc.)

**Important:**
- Agents cannot have back-and-forth with parent
- All context must be in initial Task prompt
- Agent completes work autonomously
- Returns one final report

### Error Handling

**Agents Should:**

```markdown
## Error Handling

If source file doesn't exist:
- Report error clearly to parent
- Suggest valid file paths if possible
- Do NOT proceed with invalid state

If framework cannot be detected:
- List what was checked
- Ask parent for framework in return message
- Provide sensible default if appropriate

If tool access denied:
- Explain what was attempted
- Indicate which tool was needed
- Return partial results if possible
```

**Return Clear Status:**

```markdown
## Report Format

### Status
✓ Success | ⚠ Partial Success | ✗ Failed

### Summary
[What was accomplished]

### Details
[Specific actions taken]

### Issues
[Any problems encountered]

### Recommendations
[Next steps or improvements]
```

### Multi-Agent Coordination

**Orchestrator Pattern:**

Main Claude coordinates multiple specialized agents:

```markdown
I'll use multiple agents to complete this task:

1. Use spec-analyzer agent to understand requirements
2. Use code-generator agent to create implementation
3. Use test-writer agent to add tests
4. Use doc-generator agent to add documentation
```

**Sequential vs Parallel:**

**Sequential** (dependent tasks):
```markdown
1. Run analyzer agent
2. Wait for analysis results
3. Use results to inform generator agent
4. Run generator with context from analysis
```

**Parallel** (independent tasks):
```markdown
Run these agents in parallel:
- test-writer for src/utils.ts
- test-writer for src/parser.ts
- test-writer for src/formatter.ts
```

### Testing Agents

**Validation Process:**

1. **Description Test**: Does main Claude select this agent appropriately?
2. **Tool Access**: Can agent use all granted tools? Blocked from forbidden tools?
3. **Functionality**: Does agent accomplish its purpose?
4. **Edge Cases**: How does it handle missing files, invalid input, errors?
5. **Communication**: Does it return clear, actionable results?

**Test Checklist:**

```markdown
- [ ] Agent appears in available subagents list
- [ ] Description clearly indicates when to use
- [ ] Agent invokes successfully via Task tool
- [ ] All granted tools are accessible
- [ ] Forbidden tools are blocked
- [ ] Agent completes task as expected
- [ ] Error handling is graceful
- [ ] Return message is clear and actionable
- [ ] Model selection is appropriate for task
```

### Common Agent Patterns

**Analysis Agent:**
```yaml
---
name: code-analyzer
description: Analyzes code for patterns, issues, and improvement opportunities
tools: Read, Grep, Glob
color: purple
model: sonnet
---
```

**Generation Agent:**
```yaml
---
name: boilerplate-generator
description: Generates boilerplate code for new components following project conventions
tools: Read, Write, Glob
color: blue
model: haiku
---
```

**Validation Agent:**
```yaml
---
name: spec-validator
description: Validates specifications for completeness and consistency
tools: Read
color: yellow
model: opus
---
```

**Integration Agent:**
```yaml
---
name: api-fetcher
description: Fetches and processes data from external APIs
tools: WebFetch, Read, Write
color: orange
model: sonnet
---
```

**Orchestration Agent:**
```yaml
---
name: workflow-coordinator
description: Coordinates multiple specialized agents for complex workflows
tools: Task, Read, Write
color: red
model: opus
---
```

### Performance Considerations

**Token Efficiency:**

Agent prompts compete with task context:

**Keep prompts concise:**
- Essential instructions only
- Link to standards docs (agent can read them)
- Use `{{standards/*}}` injection
- Avoid redundant examples

**Example Optimization:**

**Before (verbose):**
```markdown
You are an expert code reviewer with 20 years of experience.
You know TypeScript, JavaScript, Python, Go, Rust, and more.
You understand SOLID principles, design patterns, testing...

[500 more lines of generic advice]
```

**After (concise):**
```markdown
You are a code reviewer specializing in [language].

Review code for:
- Type safety
- Error handling
- Security issues

See: {{standards/global/code-review.md}} for details
```

### Version Control

**Track Agent Changes:**
- Document rationale for tool selections
- Note model choice reasoning
- Explain specialization decisions
- Test before committing

**Breaking Changes:**
- Changing tool access
- Modifying agent role
- Changing model selection
- Altering return format

When making breaking changes:
1. Consider impact on main Claude
2. Update documentation
3. Test invocation patterns
4. Communicate with team

### Examples: Good vs Bad

**Good Agent:**
```yaml
---
name: python-test-generator
description: Generates pytest test files for Python modules following project conventions
tools: Read, Write, Glob
color: green
model: sonnet
---

# Python Test Generator

You are a Python testing specialist using pytest.

## Your Task

Generate comprehensive pytest test files for given Python modules.

## Process

1. Read source module
2. Identify functions/classes to test
3. Create tests/test_[module].py
4. Include:
   - Proper imports and fixtures
   - Test cases for main functionality
   - Parametrized tests where appropriate
   - Mock external dependencies

## Guidelines

- Follow existing test patterns in tests/ directory
- Use descriptive test names: test_[function]_[scenario]
- Cover happy path + basic edge cases
- Use pytest fixtures for common setup

{{standards/testing/python-testing.md}}
```

**Bad Agent:**
```yaml
---
name: helper
description: Helps with stuff
tools: Read, Write, Edit, Bash, Task, WebFetch, Glob, Grep
---

# Helper Agent

I can help with anything you need.

Just tell me what to do and I'll figure it out.
```

**Problems with bad example:**
- Vague name and description
- Too many tools (unclear purpose)
- No specialized role
- No clear process
- Not discoverable (when would you use it?)

### Related Standards

- `@agent-os/standards/claude-code/command-design.md` - Command structure
- `@agent-os/standards/claude-code/skill-design.md` - Skill patterns
- `@agent-os/standards/global/frontmatter-conventions.md` - YAML standards
- `@agent-os/standards/claude-code/prompt-engineering.md` - Effective prompts

### References

- [Claude Code Agents Documentation](https://docs.claude.com/en/docs/claude-code/agents)
- [Task Tool Best Practices](https://docs.claude.com/en/docs/claude-code/tools#task)
