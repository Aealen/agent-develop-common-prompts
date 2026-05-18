# 规划阶段 — 详细设计：完成检查清单

## [CHECKLIST]

```
Before finalizing the detailed design, verify ALL of the following:

COMPLETENESS:
- [ ] Every architecture component from Overall Design has at least one corresponding module
- [ ] Every module is assigned to a concrete file path
- [ ] No module exists in Overall Design but is missing from Detailed Design
- [ ] No module exists in Detailed Design but is missing from Overall Design (scope creep)

INTERFACE CONTRACTS:
- [ ] All public functions have typed signatures (params + return type)
- [ ] All public functions have preconditions and postconditions defined
- [ ] All public functions have error cases enumerated
- [ ] No ambiguous types (no "any", "object", untyped parameters)

DATA STRUCTURES:
- [ ] Config schema is complete — covers all fields mentioned in Harness + business logic
- [ ] State data structure captures all checkpoint-able state
- [ ] Event schemas are defined for all event types in core/harness.md EventEmitter
- [ ] All enums are exhaustive — no uncategorized values

STATE MACHINE:
- [ ] Covers all states: stopped, ready, running, paused, error
- [ ] Every state has at least one exit transition (no dead-end states)
- [ ] Error state transitions are defined with clear next-state logic
- [ ] Guard conditions are specific (no "if appropriate" — exact boolean checks)

ERROR MATRIX:
- [ ] Every identified error scenario from architecture is covered
- [ ] Each error has a classification (RECOVERABLE / NON_RECOVERABLE / TRANSIENT)
- [ ] Max retry values are specified (not "a few times" — exact number)
- [ ] Escalation path is defined for exhausted retries

SEQUENCE DIAGRAMS:
- [ ] Happy path diagram is complete (start to finish)
- [ ] Error recovery diagram is complete
- [ ] Pause/resume diagram is complete
- [ ] Each step identifies actor, action, and data

HARNESS MAPPING:
- [ ] All 6 Harness components are mapped to concrete modules
- [ ] Each mapping has a file path
- [ ] No Harness component is left unmapped or marked as "TBD"

IMPLEMENTATION NOTES:
- [ ] Language-specific considerations are documented
- [ ] External dependencies are listed with versions
- [ ] No dependency is introduced without justification

CROSS-CHECK WITH OVERALL DESIGN:
- [ ] Goal statement matches — no scope change
- [ ] All Scope items are covered by modules
- [ ] All Integrations are reflected in modules
- [ ] All Constraints from Overall Design are respected
- [ ] All Assumptions are still valid
```
