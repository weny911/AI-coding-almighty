# AGENTS.MD

This document defines the minimum executable standards for the Agent in this repository, with the goals of clear process, explicit constraints, and efficient execution.

## Development Start Rule (Mandatory)
When a user requests development, code changes, or feature implementation, the Agent must first proactively ask the user to choose one of the following three modes:

1. `Spec-Workflow MCP`
2. `R-P-I-V`
3. `Direct Modification`

If the user does not make an explicit choice, the Agent must ask again and must not enter the coding phase directly.

Regardless of which mode is chosen, `context7` must be installed and available. If it is not installed, install it first before proceeding.

## Three Process Modes

### 1) Spec-Workflow MCP
Use when requirements are complex and require strict task decomposition and acceptance criteria.

Execution requirements:
- Check and complete MCP configuration (`spec-workflow`)
- Use Spec-Workflow to split work into atomic tasks (function/module granularity)
- Enforce strict TDD: write tests first, then code, until acceptance criteria pass (with the `tdd-workflow` skill)
- After configuration changes, prompt the user to restart the IDE

MCP config files:
- Cursor: `~/.cursor/mcp.json`
- Claude Code: `~/.claude.json`
- Codex: `~/.codex/mcp.json`

Required config:
- `spec-workflow`

### 2) R-P-I-V
Use for regular engineering tasks that require traceable process.

#### R (Research)
Output a current-state summary: current behavior, boundaries, dependencies, and risks.

#### P (Plan)
Output `PLAN.md`: goals, scope, steps, involved files, verification strategy, and risks.

#### I (Implement)
Implement only according to the confirmed plan. If deviation is needed, update `PLAN.md` first.

#### V (Verify)
Run tests/type checks/static checks and report results; if failed, return to Implement for fixes.

### 3) Direct Modification
Use for small changes, urgent fixes, or when the user explicitly asks for fast execution.

Execution requirements:
- Explain change scope and risks first
- Provide at least minimal verification results after completion (for example, commands run or manual check points)

## General Coding Standards (Retained)
- Avoid hardcoding: use constants, enums, and configuration.
- Errors must be handled explicitly; silent failure/swallowing is prohibited.
- Use the project's unified logging approach; do not use `print/console.log` as production runtime logging.
- Keep single responsibility: small files, small functions, low coupling.
- Async flows must include error paths and traceable diagnostics.
- Before submission, pass project formatting, linting, type checking, and critical tests.
- Remove dead code/unused imports and avoid duplicate implementations.
- Minimize new dependencies and explain why they are needed.

## Git and Commit Flow (Core)
- Read-first by default: prioritize `git status`, `git diff`, and `git log`.
- Do not run `push`, switch branches, or rewrite history without explicit user approval.
- Before destructive operations (such as `reset --hard`, `clean`, bulk delete), explain impact scope first.
- If unknown changes are detected, pause and ask the user.

## Preset Commands

### `sync spec`
- Sync `AGENTS.MD`, `.claude/`, `.spec-workflow/`
- Before syncing, confirm source repo: `https://gitee.com/kaimenshangbao/aicoding.git`, branch: `master`

### `install anthropics skills`
Run: `npx openskills install anthropics/skills`

### `install skills from github`
Run: `npx openskills install your-org/your-skills`

### `sync skills`
Run: `npx openskills sync`

## .gitignore Check (Before Each Operation)
The following entries must exist:
- `AGENTS.md` or `AGENTS.MD`
- `.claude/`
- `.spec-workflow/`
- `logs`

## Tool Directory Locations
Keep only directory index here; do not expand the full skill list in this file.

- Skills: `.claude/skills/`
- Agents: `.claude/agents/`
- Commands: `.claude/commands/`
- Contexts: `.claude/contexts/`
- Hooks: `.claude/hooks/`
- MCP config: `.claude/mcp-configs/`
- Agent scripts: `.agents/`
