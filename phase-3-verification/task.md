# 验证阶段：任务描述

## [TASK]

```
Given the Overall Design, Detailed Design, and Implementation code files,
perform a requirement-implementation consistency verification:

1. TRACEABILITY_MATRIX: Map every requirement in design docs → corresponding code implementation
2. MISSING_FEATURES: Requirements present in design but absent or incomplete in code
3. EXTRA_FEATURES: Code features not specified in design (scope creep)
4. INTERFACE_CONFORMANCE: Verify all interface contracts match between design and code
5. HARNESS_COMPLIANCE: Verify all 6 Harness components from core/harness.md are implemented
6. CONSTRAINT_COMPLIANCE: Verify code follows all constraints from core/constraints.md and phase-2 constraints
```

## [TASK_PARAMS]

```
Overall Design Document: {{OVERALL_DESIGN}}
Detailed Design Document: {{DETAILED_DESIGN}}
Implementation Files: {{IMPLEMENTATION_FILES}}
Target Language: {{TARGET_LANGUAGE}}
```

## [INSTRUCTIONS]

```
1. Extract all requirements from the Overall Design:
   - Goal (expanded into testable statements)
   - Scope items (each in-scope item is a requirement)
   - Architecture components (each component is a requirement)
   - Data flow steps (each step is a requirement)
   - Integration points (each integration is a requirement)
   - Constraints (each constraint is a requirement)

2. Extract all detailed requirements from the Detailed Design:
   - Module Breakdown (each module is a requirement)
   - Interface Contracts (each function signature is a requirement)
   - Data Structures (each type/enum is a requirement)
   - State Machine (each transition is a requirement)
   - Error Matrix (each error handling rule is a requirement)
   - Harness Mapping (each mapping is a requirement)

3. For each requirement, search the Implementation Files for matching code:
   - Status: FULLY_IMPLEMENTED / PARTIALLY_IMPLEMENTED / MISSING
   - Evidence: file path + line range or function name

4. For each code file/function, check if it maps to a design requirement:
   - Status: MAPPED / UNMAPPED (scope creep)

5. For interface contracts, compare signatures character by character:
   - Parameter names, types, return types, error cases

6. For Harness components, verify all 6 exist and meet core/harness.md specs.

7. For constraints, check each rule from core/constraints.md against code.

8. Compile results into the output format defined in output.md.
9. Self-check using checklist.md before finalizing.
```
