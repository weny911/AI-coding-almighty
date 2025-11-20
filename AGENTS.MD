# AGENTS.MD

READ ~/Projects/agent-scripts/{AGENTS.MD,TOOLS.MD} BEFORE ANYTHING (skip if files missing).

Peter (@steipete) owns this project. Say hi when you start!

## Agent Protocol
- Primary contact: Peter Steinberger — @steipete on X, steipete@gmail.com.
- Files may live in-repo or `~/Projects/agent-scripts`; check both.
- PR links: use `gh pr view/diff` instead of pasting URLs.
- Add notes to AGENTS only when the user says “make a note”; edit `AGENTS.MD` (ignore `CLAUDE.md` symlink).
- Outside this repo, skip `./runner` if it’s not shipped; otherwise follow the runner rules below.
- Need an upstream file? Redirect to `/tmp/`, then cherry-pick—never overwrite tracked files.
- Bug fixes: add a regression test when it makes sense.
- Keep files under ~500 LOC—refactor/split and improve quality/tests while you do it.
- Commits: Conventional Commits (`feat|fix|refactor|build|ci|chore|docs|style|perf|test`; e.g., `feat(api): add telemetry`, `chore!: drop support for iOS 16`).
- Subagents: read `docs/subagent.md` (tmux launch + agent-send).
- “Open in code”: run `code <path>`.
- “Check CI”: use `gh run list/view`; rerun/fix until green.
- Aim for end-to-end debug/test; if blocked, surface what’s missing.
- Use the tools: chrome-devtools via mcporter, Peekaboo screenshots, etc.; if something’s missing, say so.
- New dependency: audit health (recent commits, releases, adoption) and note it.
- Slash commands (`/fix`, `/commit`, etc.) live in `~/.codex/prompts/`.
- Internet: search early/often, quote exact errors, prefer 2024–2025 sources; if blocked, use Firecrawl via `pnpm mcp:*` or `mcporter`.

## Docs
- Start every session with `pnpm run docs:list` (or repo equivalent); open surfaced docs before coding and keep them open.
- Follow links until you understand the domain; honor any `Read when` hints.
- Keep notes brief; update docs whenever behavior or APIs change—no feature ships without matching docs.
- Add `read_when` hints to key docs when you change cross-cutting areas.

## PR Feedback
- Find the active PR: `gh pr view --json number,title,url --jq '"PR #\\(.number): \\(.title)\\n\\(.url)"'`.
- If asked for PR comments, fetch both top-level and inline via `gh pr view …` and `gh api repos/:owner/:repo/pulls/<num>/comments --paginate`.
- When replying, cite the fix/rationale, mention file/line, and resolve threads only after the change lands.

## Flow & Runtime
- Use the project’s package manager/runtime; no swaps without approval.
- Run commands through `./runner <cmd>` when available; skip only for read-only inspection (`rg`, `sed`, `ls`, `find`, `cat`, `tmux`, etc.). Include tests, dev servers, package scripts, and allowed git commands. For harness calls that bypass runner, set `timeout_ms >= 60000` to mimic runner’s breathing room.
- Long tasks go in tmux; no `while tmux …` or `tmux has-session` polling. Avoid `tmux wait-for`; run once, sleep briefly if needed, then inspect via direct tmux calls. Treat >10 min in tmux as a likely hang.
- If a job drags, runner will nudge you to move it into tmux.

## Build / Test
- Run the repo’s full gate (lint/typecheck/tests/docs) before handoff; keep watchers you started healthy.
- If CI is red, drive it with `gh run list/view`, rerun, fix, and push until green.
- Keep workflows observable (tmux panes, CI logs, log tails, MCP/browser helpers).
- Releases: before cutting one, read `docs/RELEASING.md`. If it’s missing, search for the best-fit release checklist and alert the user.

## Git
- Default allowed: `git status`, `git diff`, `git log`; `git push` only when the user asks.
- Forbidden unless explicit: destructive commands (`reset --hard`, `checkout --`, `clean`, `restore`, `revert`, `rm`, etc.).
- Commits here: use `./scripts/committer "msg" path…`; don’t run `git add` yourself. In repos without the helper (e.g., outside agent-scripts), use normal git—don’t copy the helper.
- “Rebase” in chat = consent to `git rebase` (and continue/abort) with `RUNNER_THE_USER_GAVE_ME_CONSENT=1`, via runner/git shim.
- Assume unfamiliar diffs belong to others; don’t delete/rename without coordination. Stop if unexpected edits appear mid-task.
- Avoid repo-wide search/replace scripts; keep edits targeted and reviewable.
- If the user types a command (e.g., “pull and push”), treat that as permission for that specific command—no extra confirmation needed.
- Don’t amend commits unless explicitly asked.
- When reviewing many files, prefer `git --no-pager diff --color=never` to see the whole patch at once.
- Multi-agent etiquette: scan `git status/diff` before editing; if someone else is mid-change, coordinate before touching shared files; ship small, reviewable commits.

## Language/Stack Notes
- Swift: use the workspace helper/daemon; validate with `swift build` + appropriate tests; keep concurrency annotations accurate; let sources regenerate artifacts.
- TypeScript: stick to the repo’s package manager, run `docs:list`, keep files small, follow existing component/data patterns.

## Critical Thinking
- Chase root cause, not band-aids—trace upstream and fix the real break.
- Unsure? Read more code first; if still blocked, ask with a short options summary.
- Flag conflicting instructions and propose the safer path.
- Write down findings in the task thread so others can follow the reasoning.

## Tools

### runner / bin/runner / scripts/runner.ts
- `runner` is the Bash entry point that routes commands through `bin/runner` (compiled from `scripts/runner.ts`) to enforce timeouts, git policy, and trash-safe deletes. Rebuild `bin/runner` via `bun build scripts/runner.ts --compile --outfile bin/runner`.
- Runner is for agents; when you show commands to users, print the raw command (without `./runner`).

### git / bin/git / scripts/git-policy.ts
- Git shim that analyzes invocations and enforces guardrails; call it via `./git` (which wraps runner) and consult `scripts/git-policy.ts` for the rules.

### scripts/committer
- Commit helper that stages exactly the files you list and creates the commit; required instead of direct `git add`/`git commit`.

### bin/docs-list / scripts/docs-list.ts
- `bin/docs-list` walks `docs/`, enforces front-matter, and prints summaries; rebuild via `bun build scripts/docs-list.ts --compile --outfile bin/docs-list`. Edit `scripts/docs-list.ts` before rebuilding.

### bin/browser-tools / scripts/browser-tools.ts
- Chrome DevTools helper without running an MCP server. Key commands: `start` (launch Chrome with remote debugging), `nav <url>` (navigate), `eval <js>` (run JS in the active tab), `screenshot` (viewport PNG to /tmp), `pick <msg>` (interactive element picker), `cookies`, `inspect`, `kill`. Launch via `bin/browser-tools --help`. Edit `scripts/browser-tools.ts`, rebuild with `./runner bun build scripts/browser-tools.ts --compile --target bun --outfile bin/browser-tools`.

### bin/sleep
- Sleep shim capped at 30 seconds; run `bin/sleep --help`.

### xcp
- Xcode project/workspace helper for managing targets, groups, files, build settings, and assets; run `xcp --help`.

### xcodegen
- Generates Xcode projects from YAML specs; run `xcodegen --help`.

### lldb
- Use `lldb` inside tmux to debug native apps; attach to the running app to inspect state.

### axe
- Simulator automation CLI for describing UI (`axe describe-ui --udid …`), tapping (`axe tap --udid … -x … -y …`), typing, and hardware buttons. Use `axe list-simulators` to enumerate devices.

### oracle
- oracle gives your agents a simple, reliable way to bundle a prompt plus the right files and hand them to another AI (GPT 5 Pro + more). Use when stuck/bugs/reviewing code.
- You must call `npx -y @steipete/oracle --help` once per session to learn syntax.

### mcporter / iterm / firecrawl / XcodeBuildMCP
- MCP launcher (`npx mcporter`) that can run any registered MCP server; pass the server name as the final arg (`npx mcporter <server>`). Common endpoints: `iterm` for TTY control, `firecrawl` for site-to-Markdown fetching, `XcodeBuildMCP` for Xcode tooling. use `npx mcporter --help` to list available servers.

### gh
- GitHub CLI for PRs, CI logs, releases, and repo queries; run `gh help`.
