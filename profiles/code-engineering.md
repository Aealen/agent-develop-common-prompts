# Profile: 代码工程类 Agent

适用于与代码仓库交互的 Agent（数据库迁移、代码重构、自动修复、代码生成等）。

---

## [PROFILE: CODE_ENGINEERING]

```
Applies to: Agents that interact with code repositories
Examples: database migration, code refactoring, auto-fix, code generation, linting automation
```

## 附加需求

### 追加到 [TASK] 部分

```
ADDITIONAL_REQUIREMENTS:
- Must include file system safety checks (path traversal prevention, symlink validation)
- Must support dry-run mode (preview all changes without applying)
- Must generate a Change Manifest listing every file to be modified with diff summary
- Must support rollback to pre-change state
- StateManager must snapshot working directory before any file modification
- Must never auto-commit — all changes require human review
- Must detect and handle merge conflicts
```

### 追加到 [OUTPUT_FORMAT] 部分

```
Additional sections in output:

## Change Safety
- Pre-flight checks: disk space, file permissions, lock files
- Backup strategy: what gets snapshotted, where, how to restore
- Rollback procedure: step-by-step commands to undo all changes

## Change Manifest
| File Path | Operation | Description | Risk Level |
|-----------|-----------|-------------|-----------|
| <path> | CREATE / MODIFY / DELETE | <what changes> | LOW / MEDIUM / HIGH |
```

### 追加到 [CONSTRAINTS] 部分

```
CODE_ENGINEERING_CONSTRAINTS:
- Never modify files outside the designated project root
- Never delete files without explicit design specification
- Always use atomic file writes (write to temp, then rename)
- Preserve file encoding and line ending style (detect before writing)
- Validate all generated code parses/compiles before writing
- Log every file operation with before/after hash
```

### 追加到 Harness 要求

```
StateManager additions:
- Must snapshot full file tree state before each batch of changes
- Must support per-file rollback (undo single file change)
- Must track change provenance (which requirement drove which change)

ErrorHandler additions:
- Must detect file system errors specifically (permission denied, disk full, file locked)
- On partial batch failure, must stop and report which files were changed vs not changed
```
