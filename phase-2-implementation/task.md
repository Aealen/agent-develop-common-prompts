# 实现阶段：任务描述

## [TASK]

```
Given an approved Detailed Design, implement the complete Agent code that includes:

1. HARNESS: Full runtime harness as specified in core/harness.md
   - LifecycleManager (state machine: init → run → pause → stop)
   - MessageLoop (polling or event-driven with backoff)
   - ErrorHandler (RECOVERABLE / NON_RECOVERABLE / TRANSIENT classification)
   - StateManager (checkpoint save/restore with atomic writes)
   - EventEmitter (structured events with schema)
   - ConfigLoader (load, validate, env override, fail fast)

2. BUSINESS_LOGIC: Core agent logic as specified in Detailed Design modules
   - Implement every module listed in Module Breakdown
   - Follow every Interface Contract signature exactly

3. WIRING: Connect harness components with business logic
   - Dependency injection or factory pattern (language-appropriate)
   - No hardcoded wiring — all via config or composition root

4. CONFIG: Configuration schema and a sample config file
   - All fields from ConfigLoader spec
   - All tunable values (retry count, intervals, thresholds)
   - Comments explaining each field

5. ENTRY_POINT: A main entry that bootstraps the Agent
   - Load config → init harness → wire components → run agent
   - Handle startup errors gracefully (fail with clear message)
   - Handle shutdown signals (SIGINT/SIGTERM or language equivalent)
```

## [TASK_PARAMS]

```
Detailed Design Document: {{DETAILED_DESIGN}}
Target Language: {{TARGET_LANGUAGE}}
Project Root: {{PROJECT_ROOT}}
Agent Type: {{AGENT_TYPE}}
```

## [INSTRUCTIONS]

```
1. Read the Detailed Design Document completely before writing any code.
2. Generate files in the following order:
   a. Config schema / sample config file
   b. Data structures (types, enums, models)
   c. Harness components:
      - EventEmitter (no dependencies)
      - ConfigLoader (depends on EventEmitter for validation errors)
      - ErrorHandler (depends on EventEmitter)
      - StateManager (depends on EventEmitter, ConfigLoader)
      - MessageLoop (depends on ErrorHandler, EventEmitter, ConfigLoader)
      - LifecycleManager (depends on all above)
   d. Business logic modules (order per dependency graph)
   e. Wiring / composition root
   f. Entry point (main)
3. Each file must compile/parse independently (no syntax errors).
4. Follow the output format defined in output.md.
5. Self-check using checklist.md before finalizing.
```
