---
description: Complete workflow from Jira task to GitHub PR - fetch details, plan, implement, review, commit, and push
argument-hint: JIRA-TASK-ID
---

# jira-to-pr:jira-to-pr

## Synopsis

```
/jira-to-pr TASK-ID
```

## Description

The `jira-to-pr` command implements a complete workflow from a Jira task to a GitHub pull request. It automates the entire development lifecycle:

1. **Fetch** - Retrieves Jira task details using `acli`
2. **Plan** - Creates a detailed `change.md` file and waits for approval
3. **Implement** - Executes the approved plan following project conventions
4. **Review** - Runs a mandatory review checklist (code, docs, build, tests)
5. **Commit** - Creates properly formatted commits with DCO sign-off and AI attribution
6. **Push** - Pushes changes and provides PR creation information

This command emphasizes safety through explicit approvals at key stages and comprehensive review checklists.

## Prerequisites

- `acli` CLI tool installed and configured for Jira access
- Git configured with user name and email
- Appropriate access to both Jira project and GitHub repository

## Implementation

### STEP 1: Fetch Jira Task Information

When a Jira task ID is provided (e.g., "CNV-82681"), fetch complete task information using `acli`:

```bash
acli jira workitem view <TASK-ID> --fields '*all'
```

Extract and summarize:
- Summary: Task title
- Description: Detailed task description
- Status: Current task status
- Assignee: Who is assigned
- Priority: Task priority
- Components: Affected components
- Labels: Any relevant labels

Present this information and confirm understanding.

### STEP 2: Create Planning Document (change.md)

Create `change.md` in the project root with this structure:

```markdown
# Change Plan: [Brief Title]

## Jira Task
- **Task ID**: [TASK-ID]
- **Summary**: [Task summary from Jira]
- **Priority**: [Priority level]

## Goal
[Clear description of what needs to be accomplished]

## Files to Modify/Create
- `path/to/file1` - [What will change]
- `path/to/file2` - [What will change]

## Technical Plan

### Step 1: [First major step]
- Detailed action 1
- Detailed action 2

### Step 2: [Second major step]
- Detailed action 1
- Detailed action 2

## Testing Strategy
- Unit tests: [What will be tested]
- Integration tests: [If applicable]
- Manual verification: [If needed]

## Build/Generation Steps Required
- [ ] List any build, generation, or compilation steps needed
- [ ] Note any dependency updates required
```

**CRITICAL**: After saving `change.md`, **STOP immediately**. Ask the user:
> "I have outlined the plan in `change.md`. Please review it. Reply with 'APPROVED' to proceed, or let me know what needs to be changed."

**WAIT** for explicit user approval. Do not continue until you receive "APPROVED" or clear affirmative.

**Trivial Changes Exception**: For single-line fixes, typo corrections, or formatting-only changes, you may skip the `change.md` step if the user explicitly confirms the change is trivial.

### STEP 3: Implementation

**ONLY** proceed after receiving "APPROVED" from the user.

1. Implement the changes exactly as outlined in `change.md`
   - Follow the step-by-step technical plan
   - Do not deviate from the approved plan without asking permission
   - If you discover issues that require plan changes, ask the user first

2. Follow project-specific conventions:
   - Check for CLAUDE.md, AGENTS.md, or CONTRIBUTING.md for project rules
   - If none exist, infer conventions from existing code
   - Follow the project's code style and testing conventions
   - Use the project's preferred tools and frameworks

### STEP 4: Review Checklist

**MANDATORY** - Complete ALL items before committing:

#### 4.1 Code Self-Review
- [ ] Read through all code changes you just implemented
- [ ] Check for common issues:
  - Logic errors or bugs
  - Security vulnerabilities (SQL injection, XSS, command injection, etc.)
  - Performance issues (inefficient algorithms, memory leaks)
  - Edge cases not handled
  - Error handling missing or incorrect
  - Code duplication that should be refactored
  - Inconsistent naming or style
  - Missing or incorrect type checks/validations
- [ ] If ANY issues are found: fix immediately and review again
- [ ] Verify the code follows the approved plan in `change.md`
- [ ] Ensure code is readable and maintainable

#### 4.2 Documentation Review
- [ ] Identify if changes require documentation updates
- [ ] Check if any of these apply:
  - New features or functionality
  - Modified behavior or configuration
  - New dependencies or build requirements
  - Changed workflows or processes
- [ ] Update relevant documentation files (README, docs/, etc.)
- [ ] Update inline code comments if needed

#### 4.3 Build & Generation Review
- [ ] Check if changes require regenerating any artifacts
- [ ] Run any project-specific build or generation commands
- [ ] Verify all generated/compiled artifacts are included in the commit
- [ ] Check for any project-specific consistency or validation steps

#### 4.4 Code Quality Review
- [ ] Run the project's test suite to verify tests pass
- [ ] Address any test failures before proceeding
- [ ] Run the project's linting/formatting tools
- [ ] Fix any linting or formatting issues
- [ ] Verify all new code has appropriate test coverage

After completing the review checklist, ask the user:
> "Implementation and review checklist are complete. Would you like me to proceed with committing and pushing the changes to GitHub (STEP 5)?"

### STEP 5: Git Workflow

**ONLY** proceed after user confirms they want to commit and push.

#### 5.1 Create or Verify Feature Branch

```bash
# Check current branch
git branch --show-current

# If on main, create a new feature branch
git checkout -b feature/[descriptive-name]
# OR for bug fixes
git checkout -b fix/[issue-description]
```

Branch naming conventions:
- `feature/[jira-id]-[short-description]` - for new features
- `fix/[jira-id]-[short-description]` - for bug fixes
- `docs/[description]` - for documentation-only changes
- `refactor/[description]` - for refactoring work

#### 5.2 Stage Changes

```bash
# Stage specific files (preferred)
git add path/to/file1.go path/to/file2.yaml

# Verify what's staged
git status
```

**IMPORTANT**: Prefer staging specific files over `git add .` or `git add -A`.

#### 5.3 Create Commit with DCO Sign-off

Commit types: `feat`, `fix`, `docs`, `test`, `build`, `refactor`, `ci`

```bash
git commit --signoff -m "$(cat <<'EOF'
type(component): subject line (max 72 chars)

Body paragraph explaining what and why (not how).
Wrap at 72 characters.

References: PROJ-12345

Co-Authored-By: <actual AI model name> <noreply@anthropic.com>
EOF
)"
```

**CRITICAL**:
- Always use `--signoff` flag (DCO requirement if project requires it)
- **MUST include AI attribution**: Use `Co-Authored-By:` or `Assisted-by:` with the actual model name

#### 5.4 Verify Commit

```bash
# Verify the commit was created correctly
git log -1 --pretty=full

# Check that DCO sign-off is present
git log -1 | grep "Signed-off-by"
```

#### 5.5 Push to Origin

```bash
# Push the feature branch to origin
git push origin feature/[branch-name]

# If this is the first push, use -u to set upstream
git push -u origin feature/[branch-name]
```

#### 5.6 Provide PR Information

After successfully pushing, provide:
1. Branch name and commit summary
2. Suggested PR title and description
3. Link to create PR (if known)

## Error Handling

### Jira Task Not Found
- Verify the task ID format (e.g., CNV-12345)
- Check if you have access to the Jira project
- Ask user to verify the task ID

### Merge Conflicts
- Inform the user about the conflict
- Ask if they want to:
  - Resolve conflicts manually
  - Rebase on latest main
  - Abandon and restart on fresh branch

### Test Failures
- Show the failing tests
- Do NOT proceed with commit
- Ask user if you should:
  - Fix the failing tests
  - Modify the implementation
  - Skip specific tests (requires justification)

### Pre-commit Hook Failures
- Show the hook error message and fix the issue
- Re-run the commit (create NEW commit, don't amend unless explicitly asked)

## Common Pitfalls

1. **NEVER skip DCO sign-off** - Always use `--signoff` flag (if project requires it)
2. **NEVER deviate from approved plan** - Ask permission first
3. **NEVER skip review checklist** - Even for "simple" changes
4. **NEVER force push** - Unless explicitly requested by user
5. **NEVER commit without testing** - Always run the project's test suite first
6. **NEVER skip generated/compiled artifacts** - Include all build outputs if required
7. **NEVER ignore project conventions** - Check CLAUDE.md, CONTRIBUTING.md, or similar files
8. **NEVER bypass pre-commit hooks** - Use `--no-verify` only if user explicitly requests it

## Return Value

The command progresses through stages:
1. Jira task summary and `change.md` creation (waits for APPROVED)
2. Implementation completion and review checklist (waits for confirmation to commit)
3. Git branch, commit hash, and PR creation information

## Examples

### Example 1: Full workflow

```
User: "/jira-to-pr CNV-82681"
AI:   Fetches task via acli → shows summary → creates change.md
AI:   "I have outlined the plan in change.md. Please review it.
       Reply with 'APPROVED' to proceed."
User: "APPROVED"
AI:   Implements changes → runs review checklist → runs tests
AI:   "Implementation and review checklist are complete. Proceed with commit?"
User: "yes"
AI:   Creates branch fix/CNV-82681-repair-vm-export →
      commits with DCO sign-off → pushes → provides PR info
```

### Example 2: Plan modification

```
User: "/jira-to-pr CNV-99999"
AI:   Fetches task → creates change.md
AI:   "I have outlined the plan in change.md. Please review..."
User: "Change step 3 to use the batch API instead"
AI:   Updates change.md with revised approach
AI:   "Updated change.md. Please review the changes."
User: "APPROVED"
AI:   Proceeds with implementation using the revised plan...
```

### Example 3: Trivial change

```
User: "/jira-to-pr CNV-55555"
AI:   Fetches task: "Fix typo in error message"
AI:   "This appears to be a trivial single-line change. Skip change.md?"
User: "yes, just fix it"
AI:   Makes change → runs review checklist → commits with DCO → pushes
```

## Arguments

- `$1` - **TASK-ID**: The Jira task identifier (e.g., CNV-12345, PROJ-6789)
