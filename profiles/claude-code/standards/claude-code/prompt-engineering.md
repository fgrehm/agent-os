## Claude Code Prompt Engineering standards

### Overview

Effective prompts are the foundation of Claude Code commands, agents, and skills. Well-engineered prompts produce consistent, high-quality results while poorly-written prompts lead to confusion and errors.

### Core Principles

**Clarity Over Cleverness:**
> Simple, direct instructions beat clever, implicit ones.

**Examples Over Explanations:**
> Show what you want, don't just describe it.

**Constraints Enable Creativity:**
> Clear boundaries help Claude make better decisions.

### Prompt Structure

**Effective Prompt Pattern:**

```markdown
# Clear Title

[1-2 sentence summary of task]

## Your Role

You are a [specific expertise] with focus on [narrow domain].

## Your Task

[Concrete, actionable task description]

## Process

1. First concrete step
2. Second concrete step
3. Third concrete step

## Guidelines

- Specific rule
- Another specific rule
- Edge case handling

## Examples

[Concrete examples showing desired output]

{{standards/relevant/standard.md}}
```

**Key Elements:**

1. **Role**: Establish expertise and perspective
2. **Task**: What needs to be accomplished
3. **Process**: Step-by-step approach
4. **Guidelines**: Rules and constraints
5. **Examples**: Concrete illustrations
6. **Standards**: Reference to detailed standards

### Instruction Clarity

**Be Specific:**

**Bad:**
```markdown
Make the code better.
```

**Good:**
```markdown
Refactor this code to:
1. Extract repeated logic into shared functions
2. Add TypeScript types to all parameters
3. Replace console.log with proper logger
4. Add JSDoc comments to public functions
```

**Be Concrete:**

**Bad:**
```markdown
Write some tests for this.
```

**Good:**
```markdown
Create a test file that:
1. Tests the happy path with valid input
2. Tests error handling with invalid input
3. Tests edge cases (empty, null, undefined)
4. Uses the project's Jest configuration
5. Follows naming pattern: [filename].test.ts
```

**Avoid Ambiguity:**

**Ambiguous:**
```markdown
Update the configuration.
```

**Clear:**
```markdown
Add the following to tsconfig.json:
- "strict": true
- "noUncheckedIndexedAccess": true
Leave all other settings unchanged.
```

### Examples

**The Power of Examples:**

Examples are often more effective than lengthy explanations.

**Instead of:**
```markdown
Write commit messages that follow conventional commit format with
a type prefix, optional scope, and imperative mood description.
```

**Show examples:**
```markdown
Write commit messages following these patterns:

feat(auth): add OAuth login support
fix(api): resolve rate limiting issue
docs(readme): update installation steps
test(utils): add edge case coverage
```

**Multiple Examples:**

Show variations to establish patterns:

```markdown
## File Naming Examples

Components:
- Button.tsx → Button.test.tsx
- UserProfile.tsx → UserProfile.test.tsx

Utilities:
- formatDate.ts → formatDate.test.ts
- parseJSON.ts → parseJSON.test.ts

Hooks:
- useAuth.ts → useAuth.test.ts
- useLocalStorage.ts → useLocalStorage.test.ts

Pattern: [filename].test.[ext]
```

### Task Decomposition

**Break Down Complex Tasks:**

**Instead of:**
```markdown
Build a user authentication system.
```

**Decompose:**
```markdown
## Phase 1: User Model
1. Create User type with email, password hash, created_at
2. Add user validation (email format, password strength)
3. Write tests for user model

## Phase 2: Authentication Logic
1. Implement password hashing with bcrypt
2. Create login function
3. Create token generation
4. Write auth tests

## Phase 3: Middleware
1. Create auth middleware
2. Add token verification
3. Write middleware tests
```

**Sequential vs Parallel:**

**Sequential Tasks:**
```markdown
Complete these steps in order:
1. Read existing code to understand structure
2. Based on structure, create test file
3. Run tests to verify they work
```

**Parallel Tasks:**
```markdown
Create these files (order doesn't matter):
- User.ts
- Product.ts
- Order.ts
```

### Constraint Definition

**Effective Constraints:**

**Tool Constraints:**
```markdown
Use ONLY these tools:
- Read: To examine existing files
- Write: To create new files
- Do NOT use Bash or Edit for this task
```

**Scope Constraints:**
```markdown
Modify ONLY the authentication logic.
Do NOT change:
- Database schema
- API endpoints
- Frontend components
```

**Format Constraints:**
```markdown
Output must be valid JSON matching this schema:
{
  "results": Array<{id: string, score: number}>,
  "total": number
}
```

**Style Constraints:**
```markdown
Follow these conventions:
- camelCase for variables
- PascalCase for types
- UPPER_SNAKE_CASE for constants
- Descriptive names over short names
```

### Conditional Logic

**Handle Variations:**

```markdown
## File Type Handling

If the file is TypeScript (.ts, .tsx):
- Add type annotations
- Use interface over type when possible
- Enable strict null checks

If the file is JavaScript (.js, .jsx):
- Add JSDoc type comments
- Use clear variable names
- Add runtime type checks
```

**Environment Handling:**

```markdown
## Environment Detection

Check package.json for test framework:
- If jest: Use Jest syntax
- If vitest: Use Vitest syntax
- If mocha: Use Mocha syntax
- If none: Ask user which to use
```

**Graceful Degradation:**

```markdown
## Fallback Strategy

1. Try to detect framework from package.json
2. If not found, check for test file patterns
3. If still unclear, use most common (Jest)
4. Add comment explaining assumption
```

### Error Handling Guidance

**Explicit Error Instructions:**

```markdown
## Error Handling

If the source file doesn't exist:
1. STOP immediately
2. Report: "Error: Source file [path] not found"
3. Suggest: "Check file path and try again"
4. Do NOT proceed with invalid state

If multiple test files match:
1. List all matches
2. Ask user which one to update
3. Wait for clarification
```

**Validation Steps:**

```markdown
Before proceeding, validate:
- [ ] Source file exists
- [ ] Destination doesn't already exist
- [ ] Required dependencies are installed
- [ ] User has write permission

If any validation fails, report error and stop.
```

### Progressive Disclosure

**Start Simple, Add Complexity:**

**Level 1 (Basic):**
```markdown
Create a test file for [source].

Include:
- Basic imports
- One test for main function
- Clear test description
```

**Level 2 (Intermediate):**
```markdown
[Level 1 content]

Additionally:
- Test edge cases (null, undefined, empty)
- Add setup/teardown if needed
- Use descriptive test names
```

**Level 3 (Advanced):**
```markdown
[Level 2 content]

For complex scenarios:
- Use test.each for parametrized tests
- Mock external dependencies
- Test async behavior properly
- Add integration tests if applicable
```

**Link to Details:**

```markdown
For basic usage, create test with:
- Import statement
- Describe block
- Test case

For advanced patterns, see: {{standards/testing/advanced.md}}
```

### Template Variables

**Common Variable Patterns:**

```markdown
Use these variables in your response:
- $FILE_PATH: Path to the file being processed
- $FUNCTION_NAME: Name of the function to test
- $FRAMEWORK: Detected test framework

Example output:
import { $FUNCTION_NAME } from '$FILE_PATH'

describe('$FUNCTION_NAME', () => {
  // tests here
})
```

**Substitution Instructions:**

```markdown
Replace placeholders:
- [COMPONENT_NAME]: Name of the component (PascalCase)
- [DESCRIPTION]: One-line component description
- [PROPS]: Comma-separated prop names

Do NOT include brackets in final output.
```

### Feedback Loops

**Iterative Refinement:**

```markdown
## Process

1. Generate initial implementation
2. Review against checklist:
   - [ ] Follows naming conventions
   - [ ] Has proper types
   - [ ] Includes error handling
   - [ ] Has tests
3. If any item unchecked, revise
4. Present final version
```

**Self-Validation:**

```markdown
After generating code, verify:
- Syntax is valid (no obvious errors)
- Imports are correct
- Types match usage
- Logic handles edge cases

If verification fails, fix issues before presenting.
```

### Agent-Specific Patterns

**For Agents:**

```markdown
You are a [specialized role].

## Your Responsibilities

Exactly these, nothing more:
1. [Specific task 1]
2. [Specific task 2]
3. [Specific task 3]

## Your Constraints

You can:
- [Allowed action 1]
- [Allowed action 2]

You cannot:
- [Forbidden action 1]
- [Forbidden action 2]

## Your Output

Return a message with:
### Summary
[What you did]

### Results
[Artifacts, analysis, etc.]

### Issues
[Problems encountered]

### Recommendations
[Suggested next steps]
```

### Skill-Specific Patterns

**For Skills:**

```markdown
# [Skill Name]

This skill [does what] and should be used when [trigger conditions].

## When to Use

Activate this skill when the user:
- Mentions [keyword 1]
- Asks about [topic]
- Requests [specific action]

## What You Do

[Concise, actionable steps]

## How to Do It

[Specific process]

For detailed reference: [link to detailed docs]
```

### Command-Specific Patterns

**For Commands:**

```markdown
# [Command Name]

[1-2 sentence description]

## Usage

/command-name [arguments]

## What This Does

1. [Step 1]
2. [Step 2]
3. [Step 3]

## Prerequisites

- [Requirement 1]
- [Requirement 2]

## Examples

/command-name arg1
→ [Expected result]

/command-name arg2
→ [Expected result]
```

### Anti-Patterns

**Avoid These Patterns:**

**Vague Instructions:**
```markdown
❌ Make it better
❌ Improve the code
❌ Fix any issues
❌ Do what makes sense
```

**Over-Explanation:**
```markdown
❌ This is important because [500 words of theory]
✅ Do X. For background, see: [link]
```

**Implicit Expectations:**
```markdown
❌ Create a proper test file
✅ Create a test file with describe blocks, test cases, and proper imports
```

**Ambiguous Scope:**
```markdown
❌ Update the configuration
✅ Add "strict": true to tsconfig.json, do not modify other settings
```

**Missing Examples:**
```markdown
❌ Follow conventional commit format
✅ Examples: feat(auth): add login, fix(api): resolve timeout
```

### Testing Prompts

**Evaluation Criteria:**

1. **Clarity Test**: Can someone unfamiliar understand what to do?
2. **Completeness Test**: Are all edge cases covered?
3. **Example Test**: Do examples clarify ambiguity?
4. **Constraint Test**: Are boundaries clear?
5. **Error Test**: Is error handling specified?

**Iteration Process:**

1. Write initial prompt
2. Test with actual command/agent/skill
3. Note where Claude gets confused
4. Add clarity at confusion points
5. Repeat until consistent results

**A/B Testing:**

```markdown
Version A: "Create tests"
Version B: "Create test file with describe blocks for each function"

Result: Version B produces more consistent output
```

### Token Efficiency

**The Conciseness Principle:**
> Every token competes with conversation history.

**Optimization Strategies:**

**Use References:**
```markdown
❌ [500 lines of coding standards]
✅ Follow: {{standards/global/coding-style.md}}
```

**Link, Don't Duplicate:**
```markdown
❌ Here are 50 examples of good commit messages...
✅ Examples: feat(auth): add login
    For more: docs/commits.md
```

**Assume Knowledge:**
```markdown
❌ TypeScript is a superset of JavaScript that adds static typing...
✅ Add TypeScript types to all parameters
```

**Progressive Disclosure:**
```markdown
Basic usage: [Core instructions]

For advanced use cases: {{workflows/advanced.md}}
```

### Related Standards

- `@agent-os/standards/claude-code/command-design.md` - Command structure
- `@agent-os/standards/claude-code/agent-design.md` - Agent patterns
- `@agent-os/standards/claude-code/skill-design.md` - Skill creation
- `@agent-os/standards/global/frontmatter-conventions.md` - YAML frontmatter

### References

- [Anthropic Prompt Engineering Guide](https://docs.anthropic.com/en/docs/prompt-engineering)
- [Claude Code Best Practices](https://docs.claude.com/en/docs/claude-code/best-practices)
