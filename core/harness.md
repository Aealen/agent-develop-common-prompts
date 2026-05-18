# Agent Runtime Harness 骨架规范

## 概述

所有通过本提示词工具包生成的 Agent 必须包含以下 6 个运行时 Harness 组件。
这些组件构成 Agent 的基础设施层，与业务逻辑解耦，确保每个 Agent 都具备生产级可运行性。

---

## [DIRECTIVE]

```
You MUST generate an Agent runtime harness that includes all 6 components below.
All components MUST communicate through well-defined interfaces.
No component may directly access another component's internal state.
Each component MUST be independently testable.
```

---

## 组件清单

### 1. LifecycleManager

管理 Agent 的全生命周期状态转换。

**必须实现的 4 个状态入口：**

| 入口 | 触发条件 | 行为 |
|------|---------|------|
| `init` | Agent 启动 | 加载配置 → 校验依赖 → 初始化所有组件 → 进入 ready 状态 |
| `run` | 收到启动指令 | 启动 MessageLoop → 进入 running 状态 |
| `pause` | 收到暂停指令 | 停止 MessageLoop → 保存 checkpoint → 进入 paused 状态 |
| `stop` | 收到停止指令 | 停止 MessageLoop → 保存 checkpoint → 释放资源 → 进入 stopped 状态 |

**状态转换规则：**

```
stopped → init → ready → run → running
running → pause → paused
paused → run → running
running → stop → stopped
paused → stop → stopped
running → error → (ErrorHandler 决定下一步)
```

**约束：**
- 禁止跳过 init 直接 run
- 同一状态重复调用必须是幂等的（不报错、不重复执行）
- 状态转换失败时必须回滚到前一个稳定状态

---

### 2. MessageLoop

Agent 的核心消息收发循环，驱动业务逻辑执行。

**必须支持两种模式之一：**

| 模式 | 适用场景 | 实现要求 |
|------|---------|---------|
| Polling | 定时拉取任务 | 带指数退避的轮询间隔，空转时 sleep |
| Event-Driven | 被动接收事件 | 事件队列 + 消费者，支持背压（backpressure） |

**必须包含：**
- 退避策略：连续空转 N 次后逐步增大间隔，上限 capped
- 优雅退出：收到 stop 信号后完成当前消息处理再退出
- 超时保护：单条消息处理超过阈值时强制中断并上报 ErrorHandler

---

### 3. ErrorHandler

统一的异常捕获、分类与恢复机制。

**错误分类：**

| 分类 | 定义 | 处理策略 |
|------|------|---------|
| `RECOVERABLE` | 临时性故障（网络超时、服务不可用） | 自动重试，指数退避，最多 N 次 |
| `NON_RECOVERABLE` | 永久性故障（配置错误、数据损坏） | 记录日志 → 进入 error 状态 → 等待人工介入 |
| `TRANSIENT` | 短暂抖动，下次大概率成功 | 单次重试，不退避 |

**必须包含：**
- 重试计数器（per error type）
- 重试耗尽后的升级策略（转为 NON_RECOVERABLE）
- 错误上下文捕获（stacktrace + 当前状态快照）
- 通过 EventEmitter 发出 `error_occurred` 事件

---

### 4. StateManager

Agent 状态的持久化与恢复。

**必须支持：**
- `save_checkpoint(data)`: 在关键节点保存状态快照
- `restore_latest()`: 从最近的 checkpoint 恢复
- `list_checkpoints()`: 列出所有可用 checkpoint（带时间戳）
- `clear_old_checkpoints(retention)`: 清理过期 checkpoint

**约束：**
- Checkpoint 写入必须是原子的（先写临时文件再 rename，或用事务）
- Checkpoint 数据必须包含版本号，恢复时做兼容性检查
- 写入操作必须是幂等的（同一 checkpoint ID 重复写入不破坏数据）
- 序列化格式由目标语言决定（JSON / pickle / gob 等）

---

### 5. EventEmitter

结构化事件通知与日志系统。

**必须发出的事件类型：**

| 事件类型 | 触发时机 | Payload |
|---------|---------|---------|
| `state_changed` | 状态转换 | {from, to, timestamp} |
| `error_occurred` | 错误发生 | {error_type, message, context, severity} |
| `task_started` | 任务开始 | {task_id, task_type, params} |
| `task_completed` | 任务完成 | {task_id, result, duration_ms} |
| `checkpoint_saved` | Checkpoint 保存 | {checkpoint_id, timestamp, size_bytes} |

**约束：**
- 事件必须结构化（有 schema），不允许自由格式字符串
- 支持同步和异步两种 listener 注册方式
- 事件发射失败不能影响主流程（catch 并记录，不 throw）

---

### 6. ConfigLoader

配置加载、校验与环境变量管理。

**必须支持：**
- 从文件加载配置（JSON / YAML / TOML，由目标语言决定）
- 环境变量覆盖（`AGENT_XXX` 前缀的环境变量覆盖同名配置项）
- 必填字段校验（init 阶段执行，缺少必填项直接 fail fast）
- 默认值机制（可选字段有合理默认值）
- 配置 schema 定义（类型、范围、枚举值校验）

**约束：**
- 配置中禁止包含明文密码/密钥（必须通过环境变量或 secrets manager）
- init 阶段完成全部校验，运行时不再修改配置
- 配置对象是只读的（init 后 immutable）

---

## 组件间通信规则

```
ConfigLoader ──(config)──→ all components (init phase)
LifecycleManager ──(state commands)──→ MessageLoop, StateManager
MessageLoop ──(errors)──→ ErrorHandler
ErrorHandler ──(retry/skip)──→ MessageLoop
ErrorHandler ──(events)──→ EventEmitter
StateManager ──(events)──→ EventEmitter
LifecycleManager ──(events)──→ EventEmitter
```

所有组件间通信通过方法调用或事件总线，禁止：
- 直接读写其他组件的内部属性
- 使用全局变量共享状态
- 绕过 EventEmitter 直接 print 日志
