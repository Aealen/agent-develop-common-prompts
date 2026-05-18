# 验证阶段：完成检查清单

## [CHECKLIST]

```
Before finalizing the verification report, verify ALL of the following:

TRACEABILITY COVERAGE:
- [ ] Every requirement in Overall Design has a traceability entry (with ID)
- [ ] Every requirement in Detailed Design has a traceability entry (with ID)
- [ ] Every traceability entry has a status (FULLY / PARTIALLY / MISSING)
- [ ] Every traceability entry has evidence (file path + location)

MISSING FEATURES:
- [ ] All MISSING items have severity classification (CRITICAL / WARNING)
- [ ] All PARTIALLY_IMPLEMENTED items describe exactly what's missing
- [ ] No design requirement is silently dropped from the report

EXTRA FEATURES:
- [ ] All code files have been checked against design (not just sampled)
- [ ] Extra features are flagged but not automatically condemned
- [ ] Each extra feature has a recommendation (keep / remove / clarify)

INTERFACE CONFORMANCE:
- [ ] All public functions in design are checked against code
- [ ] Signatures are compared character by character (types, param names, return types)
- [ ] Error cases in design are verified against error handling in code
- [ ] Mismatches have detailed diff descriptions

HARNESS COMPLIANCE:
- [ ] All 6 Harness components are individually checked
- [ ] Each component's sub-requirements are checked (not just "file exists")
- [ ] LifecycleManager: all 4 state entries + transitions verified
- [ ] MessageLoop: backoff strategy + timeout protection verified
- [ ] ErrorHandler: 3 error classifications + retry logic verified
- [ ] StateManager: atomic writes + version field + cleanup verified
- [ ] EventEmitter: all required event types verified
- [ ] ConfigLoader: validation + env override + fail fast verified

CONSTRAINT COMPLIANCE:
- [ ] All constraints from core/constraints.md are checked
- [ ] All constraints from phase-2/constraints.md are checked
- [ ] Each violation has severity classification
- [ ] Each violation has concrete evidence (code location)

ACTION ITEMS:
- [ ] All P0 items correspond to CRITICAL violations or MISSING features
- [ ] Action items are ordered by priority (P0 → P1 → P2)
- [ ] Each action item has a suggested fix (not just "fix it")
- [ ] No orphan issues — every issue maps to a traceability/constraint entry

REPORT QUALITY:
- [ ] Summary numbers add up correctly (total = full + partial + missing)
- [ ] Compliance percentage is calculated correctly
- [ ] Conclusion matches the data (PASS/CONDITIONAL/FAIL is justified)
- [ ] Report is self-contained (can be read without the design docs)
```
