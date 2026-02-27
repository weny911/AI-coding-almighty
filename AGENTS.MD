# AGENTS.MD

本文档定义 Agent 在本仓库的最小可执行规范，目标是：流程清晰、约束明确、执行高效。

## 开发启动规则（强制）
当用户提出“开发/改代码/实现需求”时，Agent 必须先主动询问用户选择以下三种模式之一：

1. `Spec-Workflow MCP`
2. `R-P-I-V`
3. `直接修改`

若用户未明确选择，默认再次确认，不得直接进入编码阶段。

## 三种流程说明

### 1) Spec-Workflow MCP
适用：需求复杂、需要严格任务拆解与验收。

执行要求：
- 检查并补齐 MCP 配置（`spec-workflow`、`context7`）
- 使用 Spec-Workflow 拆解原子任务（函数/模块粒度）
- 严格 TDD：先测后码，直到验收标准通过
- 配置变更后提示用户重启 IDE

MCP 配置文件：
- Cursor: `~/.cursor/mcp.json`
- Claude Code: `~/.claude.json`

必需配置：
- `spec-workflow`
- `context7`

### 2) R-P-I-V
适用：常规研发任务，需要过程可追溯。

#### R（Research）
输出现状摘要：当前行为、边界、依赖、风险。

#### P（Plan）
输出 `PLAN.md`：目标、范围、步骤、涉及文件、验证策略、风险。

#### I（Implement）
仅按已确认计划实施。若偏离计划，先更新 `PLAN.md` 再继续。

#### V（Verify）
运行测试/类型检查/静态检查并给出结果；失败则回到 Implement 修复。

### 3) 直接修改
适用：小改动、紧急修复、用户明确要求快速执行。

执行要求：
- 先说明改动范围与风险
- 完成后至少提供最小验证结果（如命令或手工检查点）

## 通用编码规范（保留）
- 避免硬编码：常量、枚举、配置化。
- 错误必须显式处理，禁止静默吞错。
- 使用项目统一日志方案，禁止用 `print/console.log` 作为正式运行日志。
- 保持职责单一：小文件、小函数、低耦合。
- 异步流程必须有错误路径与可追踪信息。
- 提交前需通过项目既有格式化、lint、类型检查和关键测试。
- 删除无用代码/无用 import，避免重复实现。
- 依赖新增应最小化，并说明原因。

## Git 与提交流程（核心）
- 默认只读：优先 `git status`、`git diff`、`git log`。
- 未经用户明确同意，不执行 `push`、切分支、重写历史。
- 涉及破坏性命令（如 `reset --hard`、`clean`、批量删除）前必须先说明影响范围。
- 提交优先使用：`./.agents/scripts/committer`；不存在时用 PATH 中 `committer`。
- 发现不明变更时暂停并询问用户。

## 预设指令

### `sync spec`
- 同步 `AGENTS.MD`、`.agents/`、`.spec-workflow/`
- 同步前确认源仓库：`https://gitee.com/kaimenshangbao/aicoding.git`，分支：`master`

### `install anthropics skills`
执行：`npx openskills install anthropics/skills`

### `install skills from github`
执行：`npx openskills install your-org/your-skills`

### `sync skills`
执行：`npx openskills sync`

## 规范同步
当 `.spec-workflow/steering/spec.md` 已存在有效内容时，应将本文件核心规范同步过去，保持一致。

## .gitignore 检查（每次操作前）
必须确保以下条目存在：
- `AGENTS.md` 或 `AGENTS.MD`
- `.agents/`
- `.spec-workflow/`

## 工具目录位置
仅保留目录索引，不在本文件展开完整技能清单。

- Skills: `.claude/skills/`
- Agents: `.claude/agents/`
- Commands: `.claude/commands/`
- Contexts: `.claude/contexts/`
- Hooks: `.claude/hooks/`
- MCP 配置: `.claude/mcp-configs/`
- Agent 脚本: `.agents/`
