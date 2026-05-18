# 规划阶段 — 详细设计：任务描述

## [TASK]

```
Given an approved Overall Design, produce a Detailed Design Document that includes:

1. MODULE_BREAKDOWN: Each architecture component → concrete modules with files
2. INTERFACE_CONTRACTS: Every public function/method signature with typed parameters and return types
3. DATA_STRUCTURES: All data models, enums, config schemas, event schemas
4. STATE_MACHINE: Agent lifecycle states, transitions, triggers, and guards
5. ERROR_MATRIX: Error types → classification → recovery strategy → max retry
6. SEQUENCE_DIAGRAMS: Key flows as step-by-step numbered sequences
7. HARNESS_MAPPING: How each Harness component maps to concrete modules and files
8. IMPLEMENTATION_NOTES: Language-specific considerations and library choices
```

## [TASK_PARAMS]

```
Overall Design Document: {{OVERALL_DESIGN}}
Target Language: {{TARGET_LANGUAGE}}
Agent Type: {{AGENT_TYPE}}
Project Root: {{PROJECT_ROOT}}
```

## [INSTRUCTIONS]

```
1. Read the Overall Design Document completely before starting.
2. For each architecture component, identify:
   - Which file(s) it lives in
   - What it imports (internal + external)
   - What it exports (public interface)
3. Define every public function with:
   - Full typed signature (params + return type)
   - Preconditions (what must be true before calling)
   - Postconditions (what is guaranteed after calling)
   - Error cases (what can go wrong and what happens)
4. Design the state machine as a formal transition table:
   - State × Event → Next State + Action
5. For error handling, create a complete matrix covering:
   - Every identified failure scenario
   - Its classification (RECOVERABLE / NON_RECOVERABLE / TRANSIENT)
   - Recovery action and retry limit
6. Create sequence diagrams for at least:
   - Normal happy path
   - Error recovery path
   - Pause/resume flow
7. Map all 6 Harness components to specific modules/files.
8. Add implementation notes for language-specific patterns.
9. Self-check using checklist.md before finalizing.
```
