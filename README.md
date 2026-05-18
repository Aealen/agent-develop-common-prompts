# AI Agent 智能体提示词工具包

## 概述

本工具包是一套组合式提示词模板，用于驱动 LLM 按阶段生成高质量、带 Harness 的 AI Agent 代码。

**核心特点：**
- 通用设计，不绑定特定 LLM 或编程语言
- 组合式片段，按需拼接
- 内置 Agent 运行时 Harness 规范
- 三阶段流程：规划 → 实现 → 验证

---

## 目录结构

```
agent-develop-common-prompts/
├── core/                        # [必选] 通用核心层
│   ├── harness.md               #   Agent 运行时 Harness 骨架规范（6 个组件）
│   ├── constraints.md           #   基础约束（安全、隔离、可观测性等 9 条）
│   ├── quality.md               #   代码质量标准（大小、命名、类型安全等 8 条）
│   └── language-adapters.md     #   语言适配指南（Python/TS/Java/Go 映射表）
│
├── phase-1-planning/            # [必选] 规划阶段
│   ├── overall-design/          #   整体设计子阶段
│   │   ├── role.md              #     角色设定：Senior System Architect
│   │   ├── task.md              #     任务描述：生成整体设计文档
│   │   ├── output.md            #     输出格式：目标/范围/架构/数据流/集成/约束/假设
│   │   └── checklist.md         #   # 完成检查清单
│   └── detail-design/           #   详细设计子阶段
│       ├── role.md              #     角色设定：Senior Software Engineer
│       ├── task.md              #     任务描述：生成详细设计文档
│       ├── output.md            #     输出格式：模块/接口/数据结构/状态机/错误矩阵
│       └── checklist.md         #     完成检查清单
│
├── phase-2-implementation/      # [必选] 实现阶段
│   ├── role.md                  #   角色设定：production-grade code writer
│   ├── task.md                  #   任务描述：实现完整 Agent 代码
│   ├── output.md                #   输出格式：按依赖顺序输出文件 + 实现摘要
│   ├── constraints.md           #   实现阶段特有约束（10 条）
│   └── checklist.md             #   完成检查清单
│
├── phase-3-verification/        # [必选] 验证阶段
│   ├── role.md                  #   角色设定：requirement traceability reviewer
│   ├── task.md                  #   任务描述：需求-实现一致性校验
│   ├── output.md                #   输出格式：追溯矩阵 + 合规报告
│   └── checklist.md             #   完成检查清单
│
├── profiles/                    # [可选] Agent 类型特化层
│   ├── code-engineering.md      #   代码工程类（文件操作、dry-run、rollback）
│   ├── general-automation.md    #   通用自动化类（任务队列、进度报告、摘要）
│   └── conversational.md        #   对话交互类（会话管理、上下文窗口、PII 保护）
│
└── README.md                    # 本文件
```

---

## 使用方法

### 核心概念：片段拼接

本工具包采用**组合式设计**。每个阶段由多个独立片段组成：

| 片段类型 | 文件 | 作用 |
|---------|------|------|
| 角色设定 | `role.md` | 定义 LLM 的身份、风格、行为准则 |
| 任务描述 | `task.md` | 定义要做什么，包含参数占位符和执行指令 |
| 输出格式 | `output.md` | 定义输出文档的结构模板 |
| 检查清单 | `checklist.md` | 要求 LLM 输出前自检 |
| 约束条件 | `constraints.md` | 额外的限制规则（部分阶段有） |

**拼接顺序原则：**

```
核心层 → 角色设定 → 任务描述(含参数) → 输出格式 → 约束条件 → 检查清单
```

### 输入参数

使用前需要准备以下参数，它们会填入 `task.md` 的 `{{占位符}}` 中：

| 参数 | 说明 | 示例 |
|------|------|------|
| `{{AGENT_NAME}}` | Agent 名称（英文，PascalCase） | `DataSyncAgent` |
| `{{AGENT_DESCRIPTION}}` | 功能描述（中文或英文均可，尽量具体） | "定时从 REST API 拉取用户行为数据，经清洗后写入本地 PostgreSQL" |
| `{{AGENT_TYPE}}` | Agent 类型 | `code-engineering` / `general-automation` / `conversational` / 留空 |
| `{{TARGET_LANGUAGE}}` | 目标编程语言 | `python` / `typescript` / `java` / `go` / 留空 |
| `{{ADDITIONAL_CONTEXT}}` | 额外上下文 | 项目背景、已有代码、特殊需求、性能要求等 |
| `{{OVERALL_DESIGN}}` | 整体设计文档（Phase 1.2 输入） | Phase 1.1 的输出 |
| `{{DETAILED_DESIGN}}` | 详细设计文档（Phase 2 输入） | Phase 1.2 的输出 |
| `{{IMPLEMENTATION_FILES}}` | 实现代码（Phase 3 输入） | Phase 2 的输出 |

### 人机协作流程

每个阶段完成后必须**人工审阅**，确认后才进入下一阶段：

```
Phase 1.1 整体设计 → [人工审阅] → Phase 1.2 详细设计 → [人工审阅] → Phase 2 实现 → Phase 3 验证 → [通过? 结束 : 回 Phase 2 修复]
```

---

### 各阶段拼接方式

#### Phase 1.1 — 整体设计

```
1. core/harness.md           → 提取 [DIRECTIVE] 部分
2. core/constraints.md       → 提取 [CONSTRAINTS] 部分
3. phase-1-planning/overall-design/role.md     → 提取 [ROLE] + [STYLE] + [BEHAVIOR]
4. phase-1-planning/overall-design/task.md     → 提取 [TASK] + [TASK_PARAMS] + [INSTRUCTIONS]，替换参数
5. phase-1-planning/overall-design/output.md   → 提取 [OUTPUT_FORMAT]
6. profiles/<agent_type>.md  → 如果指定了 AGENT_TYPE，追加对应 profile
7. phase-1-planning/overall-design/checklist.md → 提取 [CHECKLIST]
```

#### Phase 1.2 — 详细设计

```
1. core/harness.md           → 提取 [DIRECTIVE] 部分
2. core/constraints.md       → 提取 [CONSTRAINTS] 部分
3. phase-1-planning/detail-design/role.md       → 提取 [ROLE] + [STYLE] + [BEHAVIOR]
4. phase-1-planning/detail-design/task.md       → 提取 [TASK] + [TASK_PARAMS] + [INSTRUCTIONS]，替换参数
   └── {{OVERALL_DESIGN}} = Phase 1.1 的输出
5. phase-1-planning/detail-design/output.md     → 提取 [OUTPUT_FORMAT]
6. core/language-adapters.md → 如果指定了 TARGET_LANGUAGE，追加
7. profiles/<agent_type>.md  → 如果指定了 AGENT_TYPE，追加
8. phase-1-planning/detail-design/checklist.md  → 提取 [CHECKLIST]
```

#### Phase 2 — 实现

```
1. core/harness.md           → 完整内容
2. core/constraints.md       → 提取 [CONSTRAINTS] 部分
3. core/quality.md           → 提取 [QUALITY_STANDARDS] 部分
4. phase-2-implementation/role.md               → 提取 [ROLE] + [STYLE] + [BEHAVIOR]
5. phase-2-implementation/task.md               → 提取 [TASK] + [TASK_PARAMS] + [INSTRUCTIONS]，替换参数
   └── {{DETAILED_DESIGN}} = Phase 1.2 的输出
6. phase-2-implementation/output.md             → 提取 [OUTPUT_FORMAT]
7. phase-2-implementation/constraints.md        → 提取 [IMPLEMENTATION_CONSTRAINTS] 部分
8. core/language-adapters.md → 如果指定了 TARGET_LANGUAGE
9. profiles/<agent_type>.md  → 如果指定了 AGENT_TYPE
10. phase-2-implementation/checklist.md         → 提取 [CHECKLIST]
```

#### Phase 3 — 验证

```
1. phase-3-verification/role.md                 → 提取 [ROLE] + [STYLE] + [BEHAVIOR]
2. phase-3-verification/task.md                 → 提取 [TASK] + [TASK_PARAMS] + [INSTRUCTIONS]，替换参数
   └── {{OVERALL_DESIGN}}      = Phase 1.1 的输出
   └── {{DETAILED_DESIGN}}     = Phase 1.2 的输出
   └── {{IMPLEMENTATION_FILES}} = Phase 2 的全部输出
3. phase-3-verification/output.md               → 提取 [OUTPUT_FORMAT]
4. phase-3-verification/checklist.md            → 提取 [CHECKLIST]
```

---

### 组合规则

| 层级 | 必选/可选 | 说明 |
|------|----------|------|
| `core/harness.md` | **必选** | 每次生成都必须包含，确保 Harness 一致性 |
| `core/constraints.md` | **必选** | 基础约束，每个阶段都需要 |
| `core/quality.md` | 实现阶段必选 | 代码质量标准仅在生成代码时需要 |
| `core/language-adapters.md` | 按需 | 指定 TARGET_LANGUAGE 时追加 |
| `phase-1-planning/` | **必选** | 规划阶段必须执行 |
| `phase-2-implementation/` | **必选** | 实现阶段必须执行 |
| `phase-3-verification/` | **必选** | 验证阶段必须执行 |
| `profiles/` | 可选 | 根据具体 Agent 类型按需追加 |

---

## Phase 1 完整示例

以下演示如何为 **"日志分析 Agent"** 拼接一个完整的 Phase 1.1 整体设计提示词。

### 场景设定

假设我们要设计一个 Agent，功能是：监控服务器日志文件，自动检测异常模式（如错误率突增、特定错误码频繁出现），并通过 Webhook 发送告警。

**参数准备：**

| 参数 | 值 |
|------|-----|
| `{{AGENT_NAME}}` | `LogAnomalyDetector` |
| `{{AGENT_DESCRIPTION}}` | 监控指定目录下的服务器日志文件（Nginx access log 格式），实时统计 HTTP 状态码分布。当 5xx 错误率在 5 分钟内超过阈值时，通过 Webhook 发送告警到企业通讯工具。支持暂停/恢复监控，支持配置多个监控规则。 |
| `{{AGENT_TYPE}}` | `general-automation` |
| `{{TARGET_LANGUAGE}}` | `python` |
| `{{ADDITIONAL_CONTEXT}}` | 日志文件按小时滚动，单文件约 100MB。告警需要去重，同一类告警 30 分钟内不重复发送。 |

### 拼接后的完整提示词（Phase 1.1 整体设计）

> 以下就是你实际发送给 LLM 的提示词。注意每个片段之间用 `---` 分隔，方便 LLM 理解结构。

```
你是一个生产级 AI Agent 的设计系统。请严格按照以下规范和指令执行任务。

---

[DIRECTIVE]
You MUST generate an Agent runtime harness that includes all 6 components below.
All components MUST communicate through well-defined interfaces.
No component may directly access another component's internal state.
Each component MUST be independently testable.

Components:
1. LifecycleManager: init → run → pause → stop state machine
2. MessageLoop: polling or event-driven with backoff strategy
3. ErrorHandler: recoverable vs non-recoverable classification, max retry N
4. StateManager: checkpoint at critical nodes, restore from latest checkpoint
5. EventEmitter: structured events on state change, error, completion
6. ConfigLoader: validate config at init phase, fail fast on missing required fields

---

[CONSTRAINTS]
1. SECURITY: Never hardcode credentials. All secrets via config/env.
2. ISOLATION: Each Harness component has single responsibility.
3. OBSERVABILITY: Every action must produce a traceable log entry.
4. GRACEFUL_DEGRADATION: On failure, preserve current state and emit error event.
5. NO_BUSY_WAIT: Loops must include sleep/yield/async-await mechanism.
6. IDEMPOTENCY: State writes must be idempotent for retry safety.
7. LANGUAGE_ADAPTATION: Follow target language idioms, don't force patterns.
8. CONFIG_DRIVEN: All tunable values in config, no magic numbers.
9. TESTABILITY: All components independently testable, no global mutable state.

---

[ROLE]
You are a Senior System Architect specializing in AI Agent design.
You design agents with clear boundaries, testable interfaces, and production-grade harness.
You think in components and contracts, not monoliths.

[STYLE]
- Ask clarifying questions before assuming
- Prefer composition over inheritance
- State all assumptions explicitly
- Use Chinese for explanatory text, English for technical terms and identifiers
- When uncertain, list options with trade-offs rather than picking one silently
- Never use vague qualifiers like "appropriate", "reasonable", "efficient" — be specific

[BEHAVIOR]
- If the requirement is ambiguous, output an "Open Questions" section and stop
- If the requirement is too large for one agent, propose decomposition immediately
- Always reference Harness components when designing architecture
- Always apply constraints as design boundaries

---

[TASK]
Given a user requirement description, produce an Overall Design Document that includes:

1. GOAL: One-sentence statement of what the Agent does
2. SCOPE: What is in-scope and explicitly out-of-scope
3. ARCHITECTURE: Component diagram with responsibilities
4. DATA_FLOW: Input → Processing → Output flow description
5. INTEGRATIONS: External systems/APIs the Agent interacts with
6. CONSTRAINTS: Technical and business constraints
7. ASSUMPTIONS: All assumptions made during design
8. OPEN_QUESTIONS: Items that need user clarification before proceeding

[TASK_PARAMS]
Agent Name: LogAnomalyDetector
Agent Description: 监控指定目录下的服务器日志文件（Nginx access log 格式），实时统计 HTTP 状态码分布。当 5xx 错误率在 5 分钟内超过阈值时，通过 Webhook 发送告警到企业通讯工具。支持暂停/恢复监控，支持配置多个监控规则。
Agent Type: general-automation
Target Language: python
Additional Context: 日志文件按小时滚动，单文件约 100MB。告警需要去重，同一类告警 30 分钟内不重复发送。

[INSTRUCTIONS]
1. Read the Agent Description carefully. Extract key requirements.
2. If any requirement is ambiguous, list it in OPEN_QUESTIONS and make a reasonable default assumption.
3. Design the architecture around the 6 Harness components:
   - LifecycleManager, MessageLoop, ErrorHandler, StateManager, EventEmitter, ConfigLoader
4. Apply Agent Type profile for general-automation:
   - Must support task queue with priority ordering
   - Must include progress reporting (percentage complete + ETA)
   - Must handle partial failures (continue remaining tasks after one fails)
   - Must generate an Execution Summary report on completion
   - ConfigLoader must validate all external API endpoints at init
5. Apply language-specific considerations for Python:
   - Use asyncio as concurrency model
   - Use dataclass or pydantic for data structures
   - Use custom Exception classes
6. Produce output in the format below.

---

[OUTPUT_FORMAT]

Return a Markdown document with the following sections:

# LogAnomalyDetector — 整体设计

## 1. 目标（Goal）
## 2. 范围（Scope）
  - In Scope
  - Out of Scope
## 3. 架构（Architecture）
  (Component diagram in text-based notation, e.g., mermaid or ASCII)
  ### 组件列表
  (Table: 组件 | 职责 | 关键接口)
  ### Harness 组件映射
  (Table: Harness 组件 | 在本 Agent 中的角色)
## 4. 数据流（Data Flow）
  (Step-by-step flow with numbered stages)
## 5. 外部集成（Integrations）
  (Table: 系统 | 用途 | 协议 | 数据格式 | 调用方式)
## 6. 约束（Constraints）
  ### 技术约束
  ### 业务约束
## 7. 假设（Assumptions）
## 8. 待确认问题（Open Questions）

---

[CHECKLIST]
Before finalizing the overall design, verify ALL of the following:

GOAL & SCOPE:
- [ ] Goal is one clear sentence, no ambiguity
- [ ] Scope boundary is explicit — "Out of Scope" has at least one entry
- [ ] No feature is described vaguely — each has clear input/output

ARCHITECTURE:
- [ ] Every component has exactly one responsibility
- [ ] Component interfaces are defined (input/output/dependencies)
- [ ] No component depends on another component's internal state
- [ ] All 6 Harness components are referenced in the architecture
- [ ] Component diagram is readable without additional explanation

DATA FLOW:
- [ ] Data flow has a clear start (input) and end (output)
- [ ] Every processing step maps to an identified component
- [ ] Error handling path is described (what happens on failure)

INTEGRATIONS:
- [ ] All external dependencies are listed with protocol and data format
- [ ] Failure modes of external systems are considered

CONSTRAINTS & ASSUMPTIONS:
- [ ] Technical constraints are specific (numbers, versions, limits)
- [ ] Assumptions that could invalidate the design are marked clearly
- [ ] At least one assumption or open question exists

HARNESS COMPLIANCE:
- [ ] LifecycleManager: 4 state entries (init/run/pause/stop) are accounted for
- [ ] MessageLoop: polling or event-driven mode is selected
- [ ] ErrorHandler: at least 2 error scenarios are identified
- [ ] StateManager: checkpoint needs are identified
- [ ] EventEmitter: event types are listed
- [ ] ConfigLoader: config fields are enumerated

请按照以上 [OUTPUT_FORMAT] 输出整体设计文档。输出前，先对照 [CHECKLIST] 逐项自检。
```

### LLM 预期输出

发送上述提示词后，LLM 应输出一份完整的 Markdown 整体设计文档，包含：
- 明确的一句话目标
- 清晰的范围边界（包含 In Scope 和 Out of Scope）
- 组件架构图和 6 个 Harness 组件映射
- 编号数据流
- 外部集成表
- 具体的技术/业务约束
- 显式列出的假设
- 待确认问题（如有）

**收到输出后：人工审阅，确认无误后，将输出作为 `{{OVERALL_DESIGN}}` 参数传入 Phase 1.2 详细设计。**

### Phase 1.2 详细设计（简略示例）

Phase 1.2 的拼接方式相同，区别在于：
- 角色从 Architect 切换为 Software Engineer
- 任务输入变为 Phase 1.1 的输出（填入 `{{OVERALL_DESIGN}}`）
- 输出格式更具体（模块分解、接口签名、状态机、错误矩阵等）
- 追加 `core/language-adapters.md` 的 Python 部分

**拼接后的提示词关键差异部分：**

```
...

[TASK_PARAMS]
Overall Design Document: <Phase 1.1 的完整输出粘贴在这里>
Target Language: python
Agent Type: general-automation
Project Root: ./log_anomaly_detector

[INSTRUCTIONS]
1. Read the Overall Design Document completely before starting.
2. For each architecture component, identify:
   - Which file(s) it lives in
   - What it imports (internal + external)
   - What it exports (public interface)
3. Define every public function with full typed signature, preconditions, postconditions, error cases.
4. Design the state machine as a formal transition table.
5. For error handling, create a complete error matrix.
6. Create sequence diagrams for: happy path, error recovery, pause/resume.
7. Map all 6 Harness components to specific modules/files.
8. Apply Python-specific patterns: asyncio, dataclass, custom Exceptions.

[OUTPUT_FORMAT]
# LogAnomalyDetector — 详细设计

## 1. 模块分解（Module Breakdown）
## 2. 接口契约（Interface Contracts）
## 3. 数据结构（Data Structures）
## 4. 状态机（State Machine）
## 5. 错误矩阵（Error Matrix）
## 6. 时序图（Sequence Diagrams）
## 7. Harness 映射（Harness Mapping）
## 8. 实现备注（Implementation Notes）

...
```

**收到输出后：人工审阅，确认无误后，将输出作为 `{{DETAILED_DESIGN}}` 参数传入 Phase 2 实现阶段。**

---

## 扩展指南

### 新增 Agent 类型 Profile

在 `profiles/` 下创建新文件，包含以下结构：

```markdown
# Profile: <类型名称>

## [PROFILE: <TYPE_NAME>]
Applies to: <适用场景描述>
Examples: <具体示例>

## 附加需求
### 追加到 [TASK] 部分
  <该类型特有的任务需求>

### 追加到 [OUTPUT_FORMAT] 部分
  <该类型特有的输出要求>

### 追加到 [CONSTRAINTS] 部分
  <该类型特有的约束>

### 追加到 Harness 要求
  <该类型对 Harness 组件的额外要求>
```

### 新增语言适配

在 `core/language-adapters.md` 中追加新语言列，覆盖所有映射表。

---

## 注意事项

- 提示词中 `{{参数}}` 为占位符，使用时替换为实际值
- `role.md` 中的 `[STYLE]` 指定中英混排规则：英文用于技术术语和标识符，中文用于说明性文字
- 每个 `checklist.md` 是自检清单，应在提示词末尾要求 LLM 输出前自检
- 验证阶段（Phase 3）的结果决定是否需要回到实现阶段修复问题
- 每个 `[SECTION]` 标记（如 `[ROLE]`、`[TASK]`）帮助 LLM 理解提示词结构，保留它们
- 片段间用 `---` 分隔，增强可读性
