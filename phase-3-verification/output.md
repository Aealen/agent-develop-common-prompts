# 验证阶段：输出格式

## [OUTPUT_FORMAT]

```markdown
# {{AGENT_NAME}} — 验证报告（Verification Report）

## 摘要（Executive Summary）

| 指标 | 数值 |
|------|------|
| 需求总数 | N |
| 完全实现 | N |
| 部分实现 | N |
| 缺失 | N |
| 额外功能（Scope Creep） | N |
| 整体合规率 | X% |

### 合规等级

| 等级 | 标准 |
|------|------|
| ✅ PASS | 合规率 ≥ 95%，无 CRITICAL 问题 |
| ⚠️ CONDITIONAL | 合规率 ≥ 80%，无 CRITICAL 问题 |
| ❌ FAIL | 合规率 < 80% 或存在 CRITICAL 问题 |

**结论: <PASS / CONDITIONAL / FAIL>**

---

## 1. 需求追溯矩阵（Traceability Matrix）

### 1.1 整体设计需求追溯

| 需求 ID | 需求描述（设计原文） | 实现位置 | 状态 |
|---------|-------------------|---------|------|
| OD-001 | <goal statement> | <file:line> | ✅ / ⚠️ / ❌ |
| OD-002 | <scope item> | <file:line> | ✅ / ⚠️ / ❌ |
| ... | ... | ... | ... |

### 1.2 详细设计需求追溯

| 需求 ID | 需求描述（设计原文） | 实现位置 | 状态 |
|---------|-------------------|---------|------|
| DD-001 | <module requirement> | <file:line> | ✅ / ⚠️ / ❌ |
| DD-002 | <interface contract> | <file:line> | ✅ / ⚠️ / ❌ |
| ... | ... | ... | ... |

## 2. 缺失功能（Missing Features）

| 需求 ID | 设计原文 | 预期实现位置 | 当前差距 | 严重等级 |
|---------|---------|-------------|---------|---------|
| <ID> | <quote from design> | <expected file/module> | <what's missing> | CRITICAL / WARNING |
| ... | ... | ... | ... | ... |

## 3. 额外功能（Extra Features / Scope Creep）

| 代码位置 | 功能描述 | 设计中是否存在 | 建议 |
|---------|---------|--------------|------|
| <file:line> | <what the code does> | YES / NO | <keep / remove / clarify> |
| ... | ... | ... | ... |

## 4. 接口一致性（Interface Conformance）

| 模块 | 接口名 | 设计签名 | 代码签名 | 一致？ |
|------|--------|---------|---------|--------|
| <module> | <function> | <design signature> | <code signature> | ✅ / ❌ |
| ... | ... | ... | ... | ... |

### 不一致详情

<对每个 ❌ 项，列出具体差异>

## 5. Harness 合规性（Harness Compliance）

| Harness 组件 | 设计要求 | 代码存在 | 功能完整 | 合规？ |
|-------------|---------|---------|---------|--------|
| LifecycleManager | <from harness.md> | ✅/❌ | ✅/⚠️/❌ | ✅/❌ |
| MessageLoop | <from harness.md> | ✅/❌ | ✅/⚠️/❌ | ✅/❌ |
| ErrorHandler | <from harness.md> | ✅/❌ | ✅/⚠️/❌ | ✅/❌ |
| StateManager | <from harness.md> | ✅/❌ | ✅/⚠️/❌ | ✅/❌ |
| EventEmitter | <from harness.md> | ✅/❌ | ✅/⚠️/❌ | ✅/❌ |
| ConfigLoader | <from harness.md> | ✅/❌ | ✅/⚠️/❌ | ✅/❌ |

### Harness 详情

<对每个 ⚠️/❌ 项，列出具体差距>

## 6. 约束合规性（Constraint Compliance）

| 约束 ID | 约束规则 | 违规？ | 证据 | 严重等级 |
|---------|---------|--------|------|---------|
| C-SEC-1 | Never hardcode credentials | ✅/❌ | <evidence or N/A> | CRITICAL |
| C-ISO-1 | Single responsibility | ✅/❌ | <evidence or N/A> | WARNING |
| ... | ... | ... | ... | ... |

## 7. 修复项（Action Items）

按优先级排序：

### P0 — 阻塞（必须修复）

| # | 问题 | 来源 | 修复建议 |
|---|------|------|---------|
| 1 | <description> | <traceability ID> | <suggested fix> |
| ... | ... | ... | ... |

### P1 — 重要（建议修复）

| # | 问题 | 来源 | 修复建议 |
|---|------|------|---------|
| ... | ... | ... | ... |

### P2 — 改进（酌情处理）

| # | 问题 | 来源 | 修复建议 |
|---|------|------|---------|
| ... | ... | ... | ... |
```
