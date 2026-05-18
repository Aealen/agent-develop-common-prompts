# 语言适配指南

本文件指导 LLM 如何将通用 Harness 概念映射到具体编程语言。
生成代码时，必须遵循目标语言的惯用写法，不强制套用其他语言的模式。

---

## Harness 组件语言映射

### LifecycleManager — 状态机实现

| 通用概念 | Python | TypeScript | Java | Go |
|---------|--------|-----------|------|-----|
| 状态定义 | Enum class | String literal union / Enum | Enum class | iota const + string |
| 状态转换 | dict of transitions | Map<string, string[]> | State pattern | switch + map |
| 转换校验 | method guard with raise | method guard with throw | method guard with IllegalStateTransition | method guard with panic or error return |
| 幂等性保护 | `if self._state == target: return` | `if (this.state === target) return` | same pattern | same pattern |

### MessageLoop — 消息循环实现

| 通用概念 | Python | TypeScript | Java | Go |
|---------|--------|-----------|------|-----|
| Polling 模式 | `while running: await asyncio.sleep(interval); await process()` | `while(running) { await sleep(interval); await process() }` | `ScheduledExecutorService` | `time.Ticker` + goroutine |
| Event-Driven 模式 | `asyncio.Queue` + consumer | `EventEmitter` / `RxJS` / async generator | `BlockingQueue` + consumer thread | `chan Message` + goroutine |
| 退避策略 | exponential_backoff helper function | retry utility (tenacity / custom) | resilience4j | custom backoff function |
| 优雅退出 | `asyncio.Event` for shutdown signal | `AbortController` or boolean flag | `CountDownLatch` / `CompletableFuture` | `context.WithCancel` + select |

### ErrorHandler — 错误处理实现

| 通用概念 | Python | TypeScript | Java | Go |
|---------|--------|-----------|------|-----|
| 错误分类 | Enum (RECOVERABLE / NON_RECOVERABLE / TRANSIENT) | same Enum | same Enum | same const pattern |
| 重试逻辑 | `tenacity` library or custom decorator | retry utility or custom wrapper | resilience4j Retry | custom retry loop with backoff |
| 错误上下文 | custom Exception subclass with context dict | custom Error subclass with context | custom Exception with fields | custom error struct with fields |
| 重试耗尽 | raise → LifecycleManager handles | throw → LifecycleManager handles | throw → LifecycleManager handles | return error → LifecycleManager handles |

### StateManager — 状态持久化实现

| 通用概念 | Python | TypeScript | Java | Go |
|---------|--------|-----------|------|-----|
| 序列化 | `json` / `pickle` / `msgpack` | `JSON.stringify` / `zod` schema | Jackson / Gson | `encoding/json` / `gob` |
| 原子写入 | write to `.tmp` → `os.rename()` | write to `.tmp` → `fs.rename()` | Files.write to temp → Files.move | write temp → os.Rename |
| 版本管理 | version field in checkpoint struct | version field in checkpoint type | version field in checkpoint class | version field in checkpoint struct |
| 清理策略 | `os.remove` files older than retention | `fs.unlink` with mtime check | Files.deleteIfOlder | os.Remove with ModTime check |

### EventEmitter — 事件系统实现

| 通用概念 | Python | TypeScript | Java | Go |
|---------|--------|-----------|------|-----|
| 事件注册 | dict of event_type → list[callable] | `EventEmitter` class or node EventEmitter | Observer pattern / EventBus | channel-based fan-out |
| 同步发射 | direct call listeners in loop | direct call | direct call | direct call |
| 异步发射 | `asyncio.create_task` per listener | `setImmediate` / `Promise.resolve().then` | ExecutorService submit | `go listener(event)` |
| 事件 Schema | TypedDict or dataclass | interface / type | POJO class | struct with json tags |

### ConfigLoader — 配置加载实现

| 通用概念 | Python | TypeScript | Java | Go |
|---------|--------|-----------|------|-----|
| 配置文件 | `json` / `toml` / `yaml` | `json` / `yaml` | `yaml` / `properties` | `json` / `yaml` / `toml` |
| 环境变量覆盖 | `os.getenv` with prefix mapping | `process.env` with prefix mapping | System.getenv with prefix | os.Getenv with prefix |
| Schema 校验 | `pydantic` or manual validation | `zod` or `joi` | Jakarta Validation or manual | `validator` tag + manual |
| 必填校验 | pydantic `Field(...)` or manual check | zod `.required()` or manual | `@NotNull` or manual | manual check with error return |
| 默认值 | pydantic default or `dict.get(key, default)` | zod default or `config.key ?? default` | `@DefaultValue` or manual | struct tag or manual assignment |
| Immutable | `frozen=True` dataclass or `types.MappingProxyType` | `Object.freeze()` or `as const` | unmodifiable wrapper | return copy, not reference |

---

## 语言特定注意事项

### Python
- 使用 `asyncio` 而非 `threading` 作为并发模型（除非有明确理由）
- 使用 `dataclass` 或 `pydantic` 定义数据结构
- 异常使用自定义 Exception 类，不直接 raise Exception
- 使用 `logging` 模块，不用 print

### TypeScript
- 使用 `async/await` 作为异步模型
- 优先使用 `interface` + `type` 定义数据结构
- 使用 `class` 仅在有状态需求时
- strict mode 开启，no `any`

### Java
- 使用 `CompletableFuture` 或虚拟线程（Java 21+）作为并发模型
- 使用 Record（Java 16+）定义不可变数据结构
- 遵循 SOLID 原则，但不过度设计
- 使用 SLF4J 作为日志门面

### Go
- 使用 goroutine + channel 作为并发模型
- 使用 struct 定义数据，interface 定义行为
- 错误处理用 `error` return，不用 panic
- 使用 `log/slog`（Go 1.21+）作为结构化日志
