# 代码质量标准

所有生成的 Agent 代码必须满足以下质量标准。

---

## [QUALITY_STANDARDS]

```
1. SIZE LIMITS
   - Functions: under 50 lines (excluding comments and blank lines)
   - Files: under 500 lines total
   - Modules: under 10 public exports
   - If a file exceeds limits, split by responsibility, not arbitrarily

2. NAMING
   - Variables and functions: descriptive, no single-letter names (except loop counters i/j/k)
   - Constants: UPPER_SNAKE_CASE or language equivalent
   - Boolean variables: prefixed with is_/has_/should_/can_
   - Avoid abbreviations unless universally understood (id, url, api)

3. TYPE SAFETY
   - Public APIs must have typed signatures (parameter types + return types)
   - Use the language's strongest type system available (strict mode, typed dicts, etc.)
   - Avoid `any` / `object` / untyped catch-all types
   - Enum values preferred over magic strings for status codes

4. ERROR MESSAGES
   - Must be actionable: tell WHAT failed, WHY it failed, HOW to fix it
   - Include context: which operation, which data, which step
   - Example: "Config validation failed: field 'retry_count' must be positive integer, got -1"
   - NOT: "Error occurred" or "Invalid input"

5. NO MAGIC VALUES
   - Named constants for all threshold values, timeouts, retry counts
   - Named constants for all status strings, event names
   - Exceptions: obvious values like 0, 1, true, false, empty string

6. DOCUMENTATION
   - Public functions: one-line summary comment (WHAT it does, not HOW)
   - Non-obvious logic: inline comment explaining WHY, not WHAT
   - No doc-blocks that just restate the function name
   - No multi-paragraph documentation blocks

7. DEPENDENCIES
   - Standard library first, external deps only when necessary
   - If external dep is used, state why in implementation notes
   - Pin dependency versions (lock file or explicit version in package config)
   - No transitive dependency hacks

8. CODE STRUCTURE
   - One responsibility per file
   - Import order: standard library → external deps → internal modules
   - No unused imports or dead code
   - No commented-out code blocks
```
