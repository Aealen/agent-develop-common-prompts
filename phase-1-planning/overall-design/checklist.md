# 规划阶段 — 整体设计：完成检查清单

## [CHECKLIST]

```
Before finalizing the overall design, verify ALL of the following:

GOAL & SCOPE:
- [ ] Goal is one clear sentence, no ambiguity
- [ ] Scope boundary is explicit — "Out of Scope" has at least one entry
- [ ] No feature is described vaguely — each has clear input/output

ARCHITECTURE:
- [ ] Every component has exactly one responsibility
- [ ] Component interfaces are defined (input/output/dependencies)
- [ ] No component depends on another component's internal state
- [ ] All 6 Harness components are referenced in the architecture
- [ ] Component diagram is readable without additional explanation

DATA FLOW:
- [ ] Data flow has a clear start (input) and end (output)
- [ ] Every processing step maps to an identified component
- [ ] Error handling path is described (what happens on failure)

INTEGRATIONS:
- [ ] All external dependencies are listed with protocol and data format
- [ ] Failure modes of external systems are considered
- [ ] Authentication/authorization requirements are noted

CONSTRAINTS & ASSUMPTIONS:
- [ ] Technical constraints are specific (numbers, versions, limits — not "should be fast")
- [ ] Assumptions that could invalidate the design are marked clearly
- [ ] At least one assumption or open question exists (if not, you haven't thought hard enough)

HARNESS COMPLIANCE:
- [ ] LifecycleManager: 4 state entries (init/run/pause/stop) are accounted for
- [ ] MessageLoop: polling or event-driven mode is selected
- [ ] ErrorHandler: at least 2 error scenarios are identified
- [ ] StateManager: checkpoint needs are identified
- [ ] EventEmitter: event types are listed
- [ ] ConfigLoader: config fields are enumerated
```
