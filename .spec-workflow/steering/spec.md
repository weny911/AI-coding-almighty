# AGENTS.MD

## 开发流程规范（R-P-I-V）
- 本流程为强制规范；除非用户明确要求跳过某一步，否则不得省略或调序；若跳过，必须写明原因与风险。
- 角色/产物定义：AI 执行=Agent 可自主完成；人类介入=必须等待用户确认；工具/产物=必须生成或使用的结果。
- 阶段产物要求（每阶段必须有可见输出）：
  - R 输出：现状摘要（当前行为、边界、关键依赖、风险）。
  - P 输出：`PLAN.md`（目标、范围、步骤、涉及文件、验证策略、风险）。
  - I 输出：代码变更 + 变更清单（文件/模块级别）。
  - V 输出：测试/检查命令与结果（成功或失败原因）。
- R: Research / 研究（AI 执行）
  - 扫描现有代码库，定位相关模块、入口、依赖与约束。
  - 压缩上下文：仅保留与需求直接相关的信息。
  - 输出"现状摘要"，必须可直接支撑规划与评审。
  - 使用 Reptile RepoMix 形成 Plan 输入（如果该工具/流程可用）。
- P: Plan / 规划（AI 执行 + 人类介入）
  - 基于现状摘要生成 `PLAN.md`，明确目标、范围、步骤与验证方式。
  - 人类审查计划：若存在逻辑错误或范围不清，必须返回修订 `PLAN.md`。
  - 审批通过后方可进入 Implement；未经批准不得写代码。
- I: Implement / 实施（AI 执行）
  - Agent 仅按已批准计划实施；若需偏离计划，必须先更新 `PLAN.md` 并重新审查。
  - Agent 编写代码；必要时使用 Git Worktree 并行赛马进行方案对比，但最终方案必须回归已审查计划。
  - 完成后进入 Verify；不得直接合并代码或自我批准。
- V: Verify / 验证（AI 执行 → 人类介入）
  - 生成测试并补齐类型检查/静态检查所需内容。
  - 运行自动化测试/类型检查；若报错，记录失败原因并返回 Implement 修复。
  - 验证通过后，提供验证结果摘要并请求合并确认。
  - 合并代码必须由人类确认或用户明确指示。

## Git
- 默认安全：优先使用只读命令 `git status/diff/log` 查看状态。仅在用户明确要求时执行 `push`。
- `git checkout` 仅用于 PR 审查或用户明确要求；避免擅自切换分支。
- 分支变更需用户同意，包括创建、切换、删除分支。
- 未明确禁止破坏性操作（如 `reset --hard`、`clean`、`restore`、`rm`）；若确有必要，先说明影响范围与目标。
- 提交助手：优先使用 `./.agents/scripts/committer`；若仓库无该脚本，则用 PATH 上的 `committer`（bash）。
- 不要删除/重命名意外内容；一旦发现不明文件或变更，先停下并询问用户。
- 不做仓库级的搜替脚本；编辑保持小、可审，优先局部修改。
- 避免手动 `git stash`；若 Git 在 pull/rebase 中自动 stash，可接受。
- 用户输入命令（如 "pull and push"）即视为同意执行该命令。
- 大型审查时使用：`git --no-pager diff --color=never`，便于稳定输出。
- 多代理协作时：编辑前检查 `git status/diff`；提交保持小且语义清晰。
- PR 规范（每次开发完成后，提醒 AI 和用户）：
  - 每次 PR 修改文件数不超过 10 个。
  - 每次 PR 不能跨项目/业务。
  - Feature 开发流程：基于 develop 创建 feature 分支 → 开发完成合并到 develop → 删除 feature 分支。
  - 提交 PR 前最好 rebase develop；提交后反复确认是否提交了所有应提交代码，是否误提交了不该提交的代码，发现问题及时更正。

## 语言规范说明

### 通用编码规范（适用于所有语言）
以下准则适用于所有编程语言，各语言特定规范在此基础上补充：
- **避免硬编码**：数字、字符串、路径、配置值等应使用常量、配置项、枚举或环境变量；数据库状态字段必须使用枚举表示。
- **错误处理**：必须明确处理错误，避免静默吞错；捕获具体异常类型并保留上下文信息；禁止使用裸 `catch` 或忽略异常。
- **日志规范**：使用项目统一的日志框架，禁止使用 `print`/`println`/`console.log` 等作为运行时日志；日志级别需与场景匹配。
- **代码结构**：文件/函数保持小且职责单一；复杂逻辑拆分为模块/函数，避免"巨型文件"；模块/包结构保持清晰，避免循环依赖。
- **副作用管理**：避免全局可变状态与隐式副作用；函数尽量保持纯函数特性；需要副作用时显式标注并集中管理。
- **异步代码**：异步代码必须处理错误路径；Promise/Future 链必须有错误处理机制；保证可取消与异常可追踪。
- **格式化与检查**：使用项目统一的 lint/format 工具；代码提交前必须通过格式化与静态检查；若未明确配置，先询问用户再选用工具。
- **测试要求**：测试用例需可复现，避免依赖外部网络/时间；必要时使用 fixtures/mocks；测试需覆盖关键路径。
- **代码质量**：不能有拼写错误；不能有冗余或重复代码；不能有无用的 import 和无用代码（被注释或未使用）。
- **依赖管理**：依赖变更需最小化；新增依赖需说明原因与替代方案评估。

### 各语言特定规范
- Swift：必须遵守以下编码规范（提交前自检）：
  - 使用工作区已有的 helper/daemon；变更后验证 `swift build` + 测试；保持并发属性与隔离语义正确。
  - 使用 Swift Package Manager (SPM) 管理依赖；若项目使用 CocoaPods/Carthage，遵循项目现有配置。
  - 类型安全优先：避免强制解包 `!`；优先使用可选绑定、`guard let`、`if let`；必要时使用 `??` 提供默认值。
  - 并发代码遵循 Swift 6+ 并发模型：优先使用 `async/await`；共享可变状态使用 `actor` 隔离；避免数据竞争。
  - 禁止使用 `@unchecked Sendable` 绕过并发检查；如确需使用，必须写明原因并评估风险。
  - 内存管理：避免循环引用；使用 `weak`/`unowned` 打破强引用循环；闭包捕获列表需明确。
  - 错误处理：使用 `Result<Success, Failure>` 或 `throws`；避免可选值表示错误；错误类型需具体明确。
  - 协议与泛型：优先使用协议扩展提供默认实现；泛型约束需明确；避免过度抽象。
  - SwiftUI 相关：视图保持轻量；复杂逻辑提取到 ViewModel 或 Model；使用 `@State`、`@Binding`、`@ObservedObject` 等属性包装器需明确语义。
  - 测试：使用 XCTest；异步测试使用 `async/await`；UI 测试使用 SwiftUI 预览或 XCTest UI 测试。
- TypeScript：必须遵守以下编码规范（提交前自检）：
  - 使用仓库指定的包管理器与脚本；若未明确配置，先询问用户再执行。
  - 必须开启并通过类型检查；禁止使用 `any` 绕过类型（除非用户明确允许并有充分理由）。
  - 避免 `// @ts-ignore`；如确需使用，必须写明原因并限制范围。
  - 运行 `docs:list`；文档或接口变更需同步更新对应说明。
- Python：必须遵守以下编码规范（提交前自检）：
  - 公共函数/类必须提供类型注解；避免随意使用 `Any`；类型不清时先补齐或拆分接口。
  - 资源管理使用上下文管理器（`with`/`async with`）。
  - I/O 明确编码与路径：优先 `pathlib`，避免隐式编码。
  - `__init__.py` 中不堆放复杂逻辑。
- Kotlin：必须遵守以下编码规范（提交前自检）：
  - 使用仓库配置的 `ktlint`/`detekt`；格式化与静态检查必须通过。
  - 空安全优先：避免滥用 `!!`；使用安全调用、`let`、`requireNotNull` 等替代。
  - 默认不可变：优先 `val` 与不可变集合；共享状态必须显式同步或限定作用域。
  - 状态表达清晰：使用 `sealed class`/`enum` 表达状态；避免魔法数字与隐式约定。
  - 协程遵循结构化并发：禁止 `GlobalScope`；合理选择调度器（IO/Default/Main）。
  - 必要时使用 `Result` 或显式错误类型。
  - 复杂逻辑拆分为可复用函数/扩展函数。
  - 协程相关测试使用合适的测试调度器与虚拟时间。
- Java：必须遵守以下编码规范（提交前自检）：
  - 代码提交前必须 reformat；方法体之间一个空行；代码片段之间可一个空行；不能连续超过两个空行。
  - 外部服务/工具必须封装成 service（如 RedisService、RestTemplateService）；所有访问通过 service；功能不足时先完善 service 再使用。
  - 全局共享数据结构/Util 类放 common 模块；非全局放对应 service 层目录。
  - 类组织顺序（从上到下）：静态变量、非静态变量、注入的 service/component（final，构造函数注入）、所有 public 方法、所有 private 方法。
  - Scheduler 必须加控制：1）上次调度未执行完时跳过本次；2）分布式锁，多实例时全局同一时刻只有一个执行。

## Spec Workflow
- 当 `.spec-workflow/steering/spec.md` 有内容时，需要自动将本文件规范同步至 `.spec-workflow/steering/spec.md`，确保规范一致。

## .gitignore 自动管理
- Agent 在每次操作前应检查 `.gitignore` 文件，确保以下条目存在（若不存在则自动添加）：
  - `AGENTS.md` 或 `AGENTS.MD`（忽略规范文件本身）
  - `.agents/`（忽略技能和脚本目录）
  - `.spec-workflow/`（忽略规范工作流目录）
- 此检查确保这些规范相关文件不会被意外提交到版本库，保持规范管理的隔离性。

## 预设指令
- Agent 应识别并执行以下预设指令（用户输入特定指令时自动触发对应操作）：
  - **`sync spec`**：从指定的规范源仓库和分支拉取最新记录，更新以下内容：
    - 更新 `AGENTS.md`（或 `AGENTS.MD`）为最新版本
    - 同步 `.agents/` 目录下的技能和脚本
    - 同步 `.spec-workflow/` 目录下的工作流配置
    - 执行前应确认源仓库地址和分支名称（可从环境变量或配置文件中读取，或询问用户）
    - 同步完成后应检查冲突，如有冲突需人工介入处理
    - 同步操作应记录日志，便于追踪变更历史
- 其他预设指令可根据需要扩展，遵循相同的识别与执行模式。

## Scripts 工具脚本

路径：`.agents/scripts/`

| 脚本 | 类型 | 用途 |
|------|------|------|
| `.agents/scripts/committer` | bash | Git 提交助手，按指定路径暂存并提交 |
| `.agents/scripts/browser-tools.ts` | TypeScript | Chrome DevTools 协议工具（控制浏览器、截图、执行 JS） |
| `.agents/scripts/nanobanana` | Python | Gemini Nano Banana Pro API 图片编辑 |

## Skills 技能目录

路径：`.agents/skills/`

### 工具集成
| 技能 | 用途 |
|------|------|
| `.agents/skills/brave-search/` | Brave Search API（网页搜索/内容提取） |

### Swift/iOS 开发
| 技能 | 用途 |
|------|------|
| `.agents/skills/swift-concurrency-expert/` | Swift 6.2+ 并发代码审查和修复 |
| `.agents/skills/swiftui-liquid-glass/` | SwiftUI 液体玻璃效果 |
| `.agents/skills/swiftui-performance-audit/` | SwiftUI 性能审计优化 |
| `.agents/skills/swiftui-view-refactor/` | SwiftUI 视图重构模式 |

### 性能分析
| 技能 | 用途 |
|------|------|
| `.agents/skills/native-app-performance/` | macOS/iOS 原生应用性能分析（xctrace/Instruments） |
| `.agents/skills/instruments-profiling/` | Instruments 性能分析 |

### 前端/设计
| 技能 | 用途 |
|------|------|
| `.agents/skills/frontend-design/` | 高质量前端界面设计（避免 AI 通用风格） |

### 实用工具
| 技能 | 用途 |
|------|------|
| `.agents/skills/create-cli/` | CLI 工具创建指南 |
| `.agents/skills/markdown-converter/` | Markdown 转换 |
| `.agents/skills/nano-banana-pro/` | Gemini 图片生成 |
