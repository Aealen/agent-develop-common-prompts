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
agent-prompts/
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

### 快速开始

每个阶段的使用方式：拼接该阶段的 `role + task + output + checklist` 片段，形成一个完整提示词。

#### 第一步：准备输入参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `{{AGENT_NAME}}` | Agent 名称 | `DataSyncAgent` |
| `{{AGENT_DESCRIPTION}}` | 功能描述 | "定时从 API 拉取数据并同步到本地数据库" |
| `{{AGENT_TYPE}}` | Agent 类型 | `code-engineering` / `general-automation` / `conversational` |
| `{{TARGET_LANGUAGE}}` | 目标语言 | `python` / `typescript` / `java` / `go` |
| `{{ADDITIONAL_CONTEXT}}` | 额外上下文 | 项目背景、已有代码、特殊需求 |

#### 第二步：Phase 1 — 规划阶段

**整体设计提示词拼接方式：**

```
[core/harness.md 的 [DIRECTIVE] 部分]
[core/constraints.md 的 [CONSTRAINTS] 部分]
[phase-1-planning/overall-design/role.md 的 [ROLE] + [STYLE] + [BEHAVIOR]]
[phase-1-planning/overall-design/task.md 的 [TASK] + [TASK_PARAMS] + [INSTRUCTIONS]]
[phase-1-planning/overall-design/output.md 的 [OUTPUT_FORMAT]]
[profiles/<agent_type>.md]  ← 如果指定了 AGENT_TYPE，追加对应 profile
```

将 `{{参数}}` 替换为实际值后发送给 LLM。获得输出后，**人工审阅确认**。

**详细设计提示词拼接方式：**

```
[core/harness.md 的 [DIRECTIVE] 部分]
[core/constraints.md 的 [CONSTRAINTS] 部分]
[phase-1-planning/detail-design/role.md 的 [ROLE] + [STYLE] + [BEHAVIOR]]
[phase-1-planning/detail-design/task.md 的 [TASK] + [TASK_PARAMS]]
  └── {{OVERALL_DESIGN}} = 上一步的输出
[phase-1-planning/detail-design/output.md 的 [OUTPUT_FORMAT]]
[core/language-adapters.md]  ← 如果指定了 TARGET_LANGUAGE，追加语言映射
[profiles/<agent_type>.md]  ← 如果指定了 AGENT_TYPE，追加对应 profile
```

获得输出后，**人工审阅确认**。

#### 第三步：Phase 2 — 实现阶段

```
[core/harness.md]  ← 完整内容
[core/constraints.md 的 [CONSTRAINTS] 部分]
[core/quality.md 的 [QUALITY_STANDARDS] 部分]
[phase-2-implementation/role.md 的 [ROLE] + [STYLE] + [BEHAVIOR]]
[phase-2-implementation/task.md 的 [TASK] + [TASK_PARAMS]]
  └── {{DETAILED_DESIGN}} = 上一步的输出
[phase-2-implementation/output.md 的 [OUTPUT_FORMAT]]
[phase-2-implementation/constraints.md 的 [IMPLEMENTATION_CONSTRAINTS] 部分]
[core/language-adapters.md]  ← 如果指定了 TARGET_LANGUAGE
[profiles/<agent_type>.md]  ← 如果指定了 AGENT_TYPE
```

#### 第四步：Phase 3 — 验证阶段

```
[phase-3-verification/role.md 的 [ROLE] + [STYLE] + [BEHAVIOR]]
[phase-3-verification/task.md 的 [TASK] + [TASK_PARAMS]]
  └── {{OVERALL_DESIGN}} = Phase 1 整体设计输出
  └── {{DETAILED_DESIGN}} = Phase 1 详细设计输出
  └── {{IMPLEMENTATION_FILES}} = Phase 2 的全部输出文件
[phase-3-verification/output.md 的 [OUTPUT_FORMAT]]
```

---

## 组合规则

### 必选 vs 可选

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

### 拼接顺序原则

1. **核心层先行** — `core/` 的内容总是放在提示词开头，建立基础规范
2. **角色设定第二** — 让 LLM 先进入角色
3. **任务描述第三** — 明确做什么
4. **输出格式第四** — 明确输出长什么样
5. **约束/检查清单最后** — 作为质量门控

### 人机协作节点

每个阶段完成后必须**人工审阅**：

```
Phase 1 整体设计 → [人工审阅批准] → Phase 1 详细设计 → [人工审阅批准] → Phase 2 实现 → Phase 3 验证 → [根据验证结果决定是否回到 Phase 2 修复]
```

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
