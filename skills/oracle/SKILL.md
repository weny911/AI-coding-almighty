---
name: oracle
description: Use the @steipete/oracle CLI to bundle a prompt plus the right files and get a second-model review (API or browser) for debugging, refactors, design checks, or cross-validation.
---

# Oracle (CLI) skill

Use Oracle when you want an external/second opinion with real repo context, or when you want multi-model cross-checking (e.g., ask GPT + Gemini + Claude and compare).

## Quick workflow

1. Pick the smallest relevant file set (avoid whole-repo dumps).
2. Run a dry-run to sanity-check what will be sent.
3. Run Oracle in API mode when possible; fall back to browser or `--render --copy` if automation is blocked.
4. Treat results as advisory: verify claims against the codebase and tests.

## Commands (preferred patterns)

- Show help once per session:
  - `npx -y @steipete/oracle --help`

- Preview bundle (no tokens spent):
  - `npx -y @steipete/oracle --dry-run summary -p "<what you need>" --file "<globs...>"`

- API run (best reliability; expects keys in env):
  - `npx -y @steipete/oracle -p "<task>" --file "<globs...>" --write-output oracle.out.md`

- Multi-model API run:
  - `npx -y @steipete/oracle -p "<task>" --models gpt-5.1-pro,gemini-3-pro,claude-4.5-sonnet --file "<globs...>"`

- Manual paste fallback (render assembled markdown + copy to clipboard):
  - `npx -y @steipete/oracle --render --copy -p "<task>" --file "<globs...>"`

## File selection tips

- Prefer globs that match the change surface, e.g.:
  - `--file "src/**/*.ts,docs/**/*.md"`
- Use `!` excludes to keep bundles small and avoid noise:
  - `--file "src/**,!**/node_modules/**,!**/dist/**,!**/.git/**"`
- Never attach secrets by default (`.env`, key files, auth tokens). If credentials are required to reproduce an issue, redact and share only what’s strictly necessary.

## Engines and keys

- Oracle auto-picks `api` when `OPENAI_API_KEY` is set; otherwise it may use browser automation.
- Common env vars:
  - `OPENAI_API_KEY` (OpenAI)
  - `GEMINI_API_KEY` (Gemini)
  - `ANTHROPIC_API_KEY` (Claude)
- If you need to force an engine:
  - `--engine api` or `--engine browser`

## Sessions (when API runs detach)

- List recent sessions: `npx -y @steipete/oracle status --hours 72`
- Inspect/re-render a session: `npx -y @steipete/oracle session <id> --render`

## Prompting guidance (for best signal)

- Ask for concrete artifacts: “return a minimal patch plan”, “list failing assumptions”, “propose tests”, “identify risky areas”.
- Provide constraints: runtime targets, style rules, performance budgets, and what not to change.
