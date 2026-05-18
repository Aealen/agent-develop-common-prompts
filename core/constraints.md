# 基础约束

所有通过本提示词工具包生成的 Agent 代码必须遵守以下约束。
这些约束是跨阶段、跨类型的通用规则。

---

## [CONSTRAINTS]

```
1. SECURITY
   - Never hardcode credentials, API keys, or tokens in source code
   - All secrets must come from config or environment variables
   - Sanitize all external input before processing
   - Validate file paths to prevent directory traversal
   - Use parameterized queries for any database operations

2. ISOLATION
   - Each Harness component has single responsibility
   - Components communicate through defined interfaces, never through internal state
   - Business logic must not depend on Harness internals
   - No circular dependencies between modules

3. OBSERVABILITY
   - Every action must produce a traceable log entry via EventEmitter
   - Log levels: DEBUG, INFO, WARN, ERROR — use appropriately
   - Include correlation ID in logs for request tracing
   - Never use print/stdout directly — always go through EventEmitter or logging framework

4. GRACEFUL_DEGRADATION
   - On failure, preserve current state (checkpoint) before reporting error
   - Never lose data that has been acknowledged/committed
   - Partial completion is acceptable if explicitly documented
   - Shut down in reverse initialization order

5. NO_BUSY_WAIT
   - All loops must include sleep / yield / async-await mechanism
   - Polling intervals must have configurable upper bound
   - Event-driven loops must have timeout protection

6. IDEMPOTENCY
   - State writes must be idempotent — retrying the same operation must not duplicate effects
   - Checkpoint saves with same ID must be safe to repeat
   - Task processing must handle "already processed" case gracefully

7. LANGUAGE_ADAPTATION
   - Follow target language idioms and conventions
   - Do not force patterns from other languages (e.g., don't write Java-style code in Python)
   - Use the language's standard library first, only introduce external deps when necessary
   - Error handling must use the language's native mechanism (exceptions, Result types, etc.)

8. CONFIG_DRIVEN
   - All tunable values (retry count, sleep interval, batch size, thresholds) must be in config
   - No magic numbers or hardcoded constants in business logic
   - Provide sensible defaults for all optional config fields

9. TESTABILITY
   - All components must be independently testable
   - External dependencies (network, filesystem, APIs) must be injectable/mockable
   - No global mutable state
   - Deterministic behavior given same input and config
```

---

## 约束违规处理

在验证阶段（phase-3），每条约束都会被逐项检查。
违规分为三个等级：

| 等级 | 定义 | 处理 |
|------|------|------|
| CRITICAL | 违反安全或数据完整性约束（SECURITY, IDEMPOTENCY, GRACEFUL_DEGRADATION） | 必须修复，阻塞发布 |
| WARNING | 违反代码质量或可维护性约束（ISOLATION, OBSERVABILITY, LANGUAGE_ADAPTATION） | 建议修复，不阻塞 |
| INFO | 违反风格约定（CONFIG_DRIVEN 中的 minor hardcode） | 记录，酌情修复 |
