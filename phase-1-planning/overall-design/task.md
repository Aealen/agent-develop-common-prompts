# 规划阶段 — 整体设计：任务描述

## [TASK]

```
Given a user requirement description, produce an Overall Design Document that includes:

1. GOAL: One-sentence statement of what the Agent does
2. SCOPE: What is in-scope and explicitly out-of-scope
3. ARCHITECTURE: Component diagram with responsibilities
4. DATA_FLOW: Input → Processing → Output flow description
5. INTEGRATIONS: External systems/APIs the Agent interacts with
6. CONSTRAINTS: Technical and business constraints
7. ASSUMPTIONS: All assumptions made during design
8. OPEN_QUESTIONS: Items that need user clarification before proceeding
```

## [TASK_PARAMS]

```
Agent Name: {{AGENT_NAME}}
Agent Description: {{AGENT_DESCRIPTION}}
Agent Type: {{AGENT_TYPE}}  [code-engineering | general-automation | conversational]
Target Language: {{TARGET_LANGUAGE}}
Additional Context: {{ADDITIONAL_CONTEXT}}
```

## [INSTRUCTIONS]

```
1. Read the Agent Description carefully. Extract key requirements.
2. If any requirement is ambiguous, list it in OPEN_QUESTIONS and make a reasonable default assumption.
3. Design the architecture around the 6 Harness components (core/harness.md):
   - LifecycleManager, MessageLoop, ErrorHandler, StateManager, EventEmitter, ConfigLoader
4. Apply Agent Type profile (from profiles/) if {{AGENT_TYPE}} is specified.
5. Apply language-specific considerations (from core/language-adapters.md) if {{TARGET_LANGUAGE}} is specified.
6. Produce output in the format defined in output.md.
7. Self-check using checklist.md before finalizing.
```
