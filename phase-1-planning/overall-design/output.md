# 规划阶段 — 整体设计：输出格式

## [OUTPUT_FORMAT]

```markdown
# {{AGENT_NAME}} — 整体设计

## 1. 目标（Goal）

<一句话描述 Agent 的核心功能>

## 2. 范围（Scope）

### 在范围内（In Scope）
- <功能点 1>
- <功能点 2>
- ...

### 范围外（Out of Scope）
- <明确排除的功能>
- ...

## 3. 架构（Architecture）

<使用 Mermaid 或 ASCII 绘制组件图>

### 组件列表

| 组件 | 职责 | 关键接口 |
|------|------|---------|
| <组件名> | <一句话职责> | <主要公共方法/接口> |
| ... | ... | ... |

### Harness 组件映射

| Harness 组件 | 在本 Agent 中的角色 |
|-------------|-------------------|
| LifecycleManager | <描述> |
| MessageLoop | <描述> |
| ErrorHandler | <描述> |
| StateManager | <描述> |
| EventEmitter | <描述> |
| ConfigLoader | <描述> |

## 4. 数据流（Data Flow）

<编号步骤描述>

1. Input: <输入来源和格式>
2. Processing Step 1: <处理逻辑>
3. Processing Step N: <处理逻辑>
4. Output: <输出目标和格式>

## 5. 外部集成（Integrations）

| 系统 | 用途 | 协议 | 数据格式 | 调用方式 |
|------|------|------|---------|---------|
| <系统名> | <目的> | HTTP/gRPC/CLI | JSON/XML/... | 同步/异步 |

## 6. 约束（Constraints）

### 技术约束
- <约束 1>
- ...

### 业务约束
- <约束 1>
- ...

## 7. 假设（Assumptions）

- <假设 1>
- ...

## 8. 待确认问题（Open Questions）

- [ ] <问题 1>
- [ ] <问题 2>
```
