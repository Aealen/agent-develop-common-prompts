# 实现阶段：输出格式

## [OUTPUT_FORMAT]

```
For each file, output in the following format:

--- FILE: {{relative_path}} ---
```{{language}}
// file content here
```

---

Generate files in this exact order:

1. 配置文件
   --- FILE: config/schema.{{ext}} ---       (配置类型/Schema 定义)
   --- FILE: config/default.{{ext}} ---       (示例配置文件)

2. 数据结构
   --- FILE: src/types.{{ext}} ---            (枚举、类型定义)
   --- FILE: src/models.{{ext}} ---           (数据模型)
   --- FILE: src/events.{{ext}} ---           (事件 Schema)

3. Harness 组件（按依赖顺序）
   --- FILE: src/harness/event_emitter.{{ext}} ---
   --- FILE: src/harness/config_loader.{{ext}} ---
   --- FILE: src/harness/error_handler.{{ext}} ---
   --- FILE: src/harness/state_manager.{{ext}} ---
   --- FILE: src/harness/message_loop.{{ext}} ---
   --- FILE: src/harness/lifecycle_manager.{{ext}} ---

4. 业务逻辑模块
   --- FILE: src/business/{{module}}.{{ext}} ---  (每个模块一个文件)

5. 接线/组装
   --- FILE: src/wiring.{{ext}} ---           (组合根 / 依赖注入)

6. 入口点
   --- FILE: main.{{ext}} ---                 (启动入口)

===

After all files, output a summary section:

## 实现摘要（Implementation Summary）

- 总文件数: N
- 预估总行数: ~M
- 外部依赖:
  | 依赖 | 版本 | 用途 |
  |------|------|------|
  | ... | ... | ... |

- 启动命令: <how to start the agent>
- 配置说明: <how to configure before first run>
```
