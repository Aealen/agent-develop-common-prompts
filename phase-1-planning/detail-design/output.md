# 规划阶段 — 详细设计：输出格式

## [OUTPUT_FORMAT]

```markdown
# {{AGENT_NAME}} — 详细设计

## 1. 模块分解（Module Breakdown）

| 模块 | 文件路径 | 职责 | 依赖 |
|------|---------|------|------|
| <模块名> | <path/to/file.ext> | <一句话职责> | <依赖的其他模块> |

<每个模块附加说明：为什么这样拆分，是否可独立测试>

## 2. 接口契约（Interface Contracts）

### 模块: <模块名>

#### `<函数/方法名>`

```
签名: function_name(param1: Type1, param2: Type2) -> ReturnType
前置条件: <调用前必须满足的条件>
后置条件: <调用后保证的状态>
错误情况:
  - <ErrorType1>: <触发条件> → <处理方式>
  - <ErrorType2>: <触发条件> → <处理方式>
```

<对每个公共函数/方法重复以上模板>

## 3. 数据结构（Data Structures）

### 配置 Schema

```<language>
// Config type/class/struct definition
```

### 状态数据

```<language>
// State type/class/struct definition
```

### 事件 Schema

```<language>
// Event type/class/struct definition for each event type
```

### 枚举类型

```<language>
// Enum definitions: ErrorClassification, AgentState, TaskStatus, etc.
```

## 4. 状态机（State Machine）

### 状态转换表

| 当前状态 | 触发事件 | 目标状态 | 执行动作 | 守卫条件 |
|---------|---------|---------|---------|---------|
| stopped | init_cmd | ready | load_config, validate_deps | config valid |
| ready | run_cmd | running | start_message_loop | — |
| running | pause_cmd | paused | stop_loop, save_checkpoint | — |
| paused | run_cmd | running | restore_checkpoint, start_loop | — |
| running | stop_cmd | stopped | stop_loop, save_checkpoint, cleanup | — |
| paused | stop_cmd | stopped | save_checkpoint, cleanup | — |
| running | error_occurred | <depends> | handle_error | see error matrix |

### 状态图（Mermaid 或文字描述）

<状态转换可视化>

## 5. 错误矩阵（Error Matrix）

| 错误场景 | 错误类型 | 分类 | 恢复策略 | 最大重试 | 升级处理 |
|---------|---------|------|---------|---------|---------|
| <场景描述> | <ErrorType> | RECOVERABLE / NON_RECOVERABLE / TRANSIENT | <具体动作> | <N> | <耗尽后处理> |

## 6. 时序图（Sequence Diagrams）

### 6.1 正常流程（Happy Path）

| 步骤 | 参与者 | 动作 | 数据 |
|------|--------|------|------|
| 1 | LifecycleManager | init() | config |
| 2 | ConfigLoader | validate(config) | config → validation_result |
| ... | ... | ... | ... |
| N | EventEmitter | emit(task_completed) | {task_id, result} |

### 6.2 错误恢复流程

<同上格式>

### 6.3 暂停/恢复流程

<同上格式>

## 7. Harness 映射（Harness Mapping）

| Harness 组件 | 实现模块 | 文件路径 | 关键说明 |
|-------------|---------|---------|---------|
| LifecycleManager | <模块名> | <路径> | <补充> |
| MessageLoop | <模块名> | <路径> | <补充> |
| ErrorHandler | <模块名> | <路径> | <补充> |
| StateManager | <模块名> | <路径> | <补充> |
| EventEmitter | <模块名> | <路径> | <补充> |
| ConfigLoader | <模块名> | <路径> | <补充> |

## 8. 实现备注（Implementation Notes）

### 语言特性
- <目标语言的特殊考虑>

### 外部依赖
| 依赖 | 版本 | 用途 | 替代方案 |
|------|------|------|---------|
| <库名> | <版本> | <用途> | <如有替代> |

### 性能考虑
- <已知的性能瓶颈及应对策略>
```
