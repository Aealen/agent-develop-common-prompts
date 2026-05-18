# 实现阶段：特有约束

## [IMPLEMENTATION_CONSTRAINTS]

```
1. FILE_ORDER
   - Generate files in dependency order — no forward references
   - A file must not import a file that appears after it in the output
   - If circular dependency is unavoidable, extract the shared part into a separate file

2. NO_PLACEHOLDERS
   - Every function must have a real, complete implementation
   - NO: TODO, FIXME, stub, pass, NotImplementedError, "implement later", ...
   - If a function is complex, break it into smaller helper functions rather than leaving it incomplete
   - Error handling must be real — no catch-and-ignore blocks

3. NO_MOCK_IN_PROD
   - Do not embed mock data, test data, or hardcoded test values in production code
   - Use config for testability — all external values configurable
   - Sample config file is separate from production code

4. EXPLICIT_ERRORS
   - All caught exceptions must be routed through ErrorHandler
   - Never catch an exception and silently ignore it
   - Never catch a broad exception type without re-raising unexpected ones
   - Use specific error types from the Error Matrix in Detailed Design

5. HARNESS_FIRST
   - Harness components must work independently of business logic
   - Business logic must not bypass Harness components (e.g., must not print directly)
   - If business logic needs logging, use EventEmitter — not print/log directly
   - If business logic needs state persistence, use StateManager — not raw file I/O

6. CONFIG_DRIVEN
   - All tunable values (retry count, sleep interval, batch size, timeouts, thresholds)
     MUST be in config — not hardcoded
   - Named constants for fixed values (like state names, event names)
   - If unsure whether something should be configurable, make it configurable

7. IMPORTABLE
   - Each module must be importable without side effects
   - No module-level code that executes on import (no top-level I/O, network calls)
   - Use functions/classes — instantiate explicitly in wiring or main

8. NO_ORPHAN_CODE
   - Every function/class must be used by at least one other module
   - If a utility function is only potentially useful, do not generate it
   - Dead code detection: trace from main entry — unreachable code must not exist

9. WIRING_ISOLATION
   - Wiring/composition root is the ONLY place that knows about all modules
   - Individual modules must not import each other's concrete implementations
   - Use interfaces/protocols for cross-module dependencies

10. ENTRY_POINT_SIMPLICITY
    - main() should be under 30 lines
    - main() does: load config → create harness → wire → start lifecycle
    - All logic is in modules, not in main
```
