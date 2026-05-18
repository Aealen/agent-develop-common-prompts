# 实现阶段：完成检查清单

## [CHECKLIST]

```
Before finalizing implementation, verify ALL of the following:

FILE COMPLETENESS:
- [ ] All files listed in Detailed Design Module Breakdown are generated
- [ ] Config schema file and sample config file both exist
- [ ] Entry point (main) file exists
- [ ] No file referenced in imports is missing from output

INTERFACE CONFORMANCE:
- [ ] Every public function matches the Detailed Design's Interface Contracts
- [ ] Function signatures match exactly (param names, types, return types)
- [ ] No extra public functions that don't exist in the design
- [ ] No missing public functions that exist in the design

HARNESS:
- [ ] LifecycleManager: init/run/pause/stop all implemented
- [ ] MessageLoop: has backoff strategy and timeout protection
- [ ] ErrorHandler: classifies RECOVERABLE/NON_RECOVERABLE/TRANSIENT
- [ ] StateManager: atomic checkpoint writes with version field
- [ ] EventEmitter: all required event types defined in schema
- [ ] ConfigLoader: validates required fields, supports env override, fail fast

LIFECYCLE WIRING:
- [ ] init → run → pause → stop state machine is fully connected
- [ ] Error recovery paths call ErrorHandler (not silent catch)
- [ ] State checkpoint happens at critical nodes (before risky operations)
- [ ] Shutdown signal (SIGINT/SIGTERM) triggers graceful stop

CODE QUALITY:
- [ ] No hardcoded credentials, URLs, or magic numbers
- [ ] No TODO/FIXME/stub/placeholder in any file
- [ ] No catch-and-ignore error blocks
- [ ] No print/log statements outside of EventEmitter
- [ ] No module-level side effects on import
- [ ] Functions under 50 lines, files under 500 lines

ENTRY POINT:
- [ ] main() successfully bootstraps the full agent
- [ ] main() handles startup errors with clear messages
- [ ] main() handles shutdown signals gracefully
- [ ] main() is under 30 lines

DEPENDENCY ORDER:
- [ ] No forward references (all imports resolve to earlier files)
- [ ] No circular imports
- [ ] External dependencies match Implementation Notes from Detailed Design

COMPILATION:
- [ ] Code compiles/parses without syntax errors
- [ ] All types resolve correctly (no undefined types)
- [ ] All imports resolve correctly (no missing imports)
```
