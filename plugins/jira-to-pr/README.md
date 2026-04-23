# Jira to PR Plugin

A Claude Code plugin that automates the complete workflow from Jira task to GitHub pull request.

## Overview

The `jira-to-pr` plugin streamlines the development process by:

1. **Fetching** Jira task details automatically using `acli`
2. **Planning** changes in a structured `change.md` file
3. **Implementing** approved changes following project conventions
4. **Reviewing** code through a comprehensive checklist
5. **Committing** with proper DCO sign-off and AI attribution
6. **Pushing** to GitHub with PR creation guidance

## Installation

1. Clone this repository or copy the `jira-to-pr` plugin folder to your Claude Code plugins directory:
   ```bash
   cp -r plugins/jira-to-pr ~/.claude/plugins/
   ```

2. Restart Claude Code or reload plugins

## Prerequisites

- **acli**: Atlassian CLI tool installed and configured
  - Installation: https://bobswift.atlassian.net/wiki/spaces/ACLI/overview
  - Configuration: `acli jira login --url https://your-jira-instance.com`
- **Git**: Configured with user name and email
- **GitHub access**: Appropriate permissions for the target repository

## Usage

```bash
/jira-to-pr TASK-ID
```

Example:
```bash
/jira-to-pr CNV-82681
```

The command will guide you through each step with explicit approval gates:
- After creating `change.md`, you must reply with "APPROVED" to proceed
- After implementation and review, you must confirm to commit and push

## Workflow Stages

### Stage 1: Fetch & Plan
- Retrieves complete Jira task information
- Creates detailed `change.md` planning document
- **STOPS** and waits for your approval

### Stage 2: Implement
- Only proceeds after "APPROVED" confirmation
- Follows the plan exactly as outlined
- Adheres to project conventions (CLAUDE.md, CONTRIBUTING.md, etc.)

### Stage 3: Review
- Mandatory review checklist:
  - Code self-review (security, performance, edge cases)
  - Documentation updates
  - Build/generation steps
  - Test suite and linting
- **STOPS** and waits for commit confirmation

### Stage 4: Commit & Push
- Creates feature/fix branch if needed
- Commits with DCO sign-off and AI attribution
- Pushes to origin
- Provides PR creation information

## Features

- **Safety-first**: Multiple approval gates prevent premature commits
- **Comprehensive**: Covers entire workflow from task to PR
- **Flexible**: Handles plan modifications and trivial changes
- **Compliant**: Automatic DCO sign-off and AI attribution
- **Error handling**: Graceful handling of conflicts, test failures, and hook failures

## Configuration

No additional configuration required beyond the prerequisites. The plugin respects:
- Project-specific files: `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`
- Git configuration: `user.name`, `user.email`
- Jira access: Configured via `acli`

## Examples

See the [command documentation](commands/jira-to-pr.md) for detailed examples including:
- Full workflow execution
- Plan modification during review
- Handling trivial changes

## License

Apache-2.0
