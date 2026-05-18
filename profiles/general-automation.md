# Profile: 通用自动化类 Agent

适用于编排自动化任务的 Agent（数据处理、文档生成、API 工作流、定时任务等）。

---

## [PROFILE: GENERAL_AUTOMATION]

```
Applies to: Agents that orchestrate automated tasks
Examples: data pipeline, report generation, API orchestration, batch processing, ETL
```

## 附加需求

### 追加到 [TASK] 部分

```
ADDITIONAL_REQUIREMENTS:
- Must support task queue with priority ordering
- Must include progress reporting (percentage complete + ETA)
- Must handle partial failures (continue remaining tasks after one fails)
- Must generate an Execution Summary report on completion
- ConfigLoader must validate all external API endpoints at init
- Must support task resume from last successful checkpoint
```

### 追加到 [OUTPUT_FORMAT] 部分

```
Additional sections in output:

## Task Queue Design
| Task Type | Priority Range | Concurrency | Timeout | Retry Policy |
|-----------|---------------|-------------|---------|-------------|
| <type> | 1-10 | sequential/parallel | <seconds> | <retry config> |

## Progress Reporting
- Metrics tracked: total_tasks, completed, failed, in_progress, remaining
- Report format: structured JSON event emitted at configurable interval
- ETA calculation: moving average of task completion time

## Execution Summary Format
- Start time, end time, total duration
- Tasks: total / succeeded / failed / skipped
- Resource usage: peak memory, total API calls, total data processed
- Failed task details: task_id, error, recovery attempted (yes/no)
```

### 追加到 [CONSTRAINTS] 部分

```
GENERAL_AUTOMATION_CONSTRAINTS:
- Never process more than N tasks concurrently (configurable, default based on resource limits)
- Never retry a task more than configured max without human escalation
- Must rate-limit external API calls (configurable requests-per-second)
- Must validate input data schema before processing (fail fast on malformed input)
- Must track and report resource consumption (memory, network, disk)
- All temporary files must be cleaned up on completion or failure
```

### 追加到 Harness 要求

```
MessageLoop additions:
- Must support both continuous polling and batch-triggered modes
- Must implement task prioritization in the message queue
- Must emit progress events at configurable intervals

StateManager additions:
- Must persist task completion state (which tasks done, which pending)
- Must support resume from task queue state after restart

ErrorHandler additions:
- Must distinguish task-level failure from system-level failure
- Task-level failure: log, mark task failed, continue queue
- System-level failure: stop queue, save state, escalate
```
