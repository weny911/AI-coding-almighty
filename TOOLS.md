# TOOLS

Edit guidance: keep the actual tool list inside the `<tools></tools>` block below so downstream AGENTS syncs can copy the block contents verbatim (without wrapping twice).

<tools>
- `runner`: Bash shim that routes every command through Bun guardrails (timeouts, git policy, safe deletes).
- `git` / `bin/git`: Git shim that forces git through the guardrails; use `./git --help` to inspect.
- `scripts/committer`: Stages the files you list and creates the commit safely.
- `scripts/docs-list.ts`: Walks `docs/`, enforces front-matter, prints summaries; run `tsx scripts/docs-list.ts`.
- `bin/browser-tools`: Compiled Chrome helper for remote control/screenshot/eval—use the binary (`bin/browser-tools --help`). Source lives in `scripts/browser-tools.ts`; edit there before rebuilding.
- `scripts/runner.ts`: Bun implementation backing `runner`; run `bun scripts/runner.ts --help`.
- `bin/sleep`: Sleep shim that enforces the 30s ceiling; run `bin/sleep --help`.
- `xcp`: Xcode project/workspace helper; run `xcp --help`.
- `oracle`: Ask a smart AI to review code and find bugs; you must call `npx -y @steipete/oracle --help` first.
- `mcporter`: MCP launcher for any registered MCP server; run `npx mcporter`.
- `iterm`: Full TTY terminal via MCP; run `npx mcporter iterm`.
- `firecrawl`: MCP-powered site fetcher to Markdown; run `npx mcporter firecrawl`.
- `XcodeBuildMCP`: MCP wrapper around Xcode tooling; run `npx mcporter XcodeBuildMCP`.
- `gh`: GitHub CLI for PRs, CI logs, releases, repo queries; run `gh help`.
</tools>
