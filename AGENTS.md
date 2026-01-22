# x.AI.Coder.2.0 用户调用指南

## Agent 列表

本项目包含 5 个 Agent：

| Agent | 功能 |
|-------|------|
| rulesFQA | 查询转换规则 |
| rule_refinement | 合并转换规则 |
| transcode | 转换单个文件组 |
| review | 审查代码 |
| fix | 修复代码 |

---

## 调用方式

### 场景 1：询问规则转换问题

**触发**：用户询问规则转换相关的问题

**处理**：调用 `rulesFQA` agent

```markdown
task(
  subagent_type="rulesFQA",
  description="查询转换规则",
  prompt="WinForms 的 LayoutVisibility.Never 在 WPF 中应该怎么转换？"
)
```

### 场景 2：合并规则

**触发**：用户要求进行规则合并

**处理**：调用 `rule_refinement` agent

```markdown
task(
  subagent_type="rule_refinement",
  description="合并转换规则",
  prompt="帮我对比 winform 和 wpf 文件夹，总结并完善转换规则"
)
```

### 场景 3：开始转换

**触发**：用户说"开始转换"

**处理流程**：

#### 步骤 1：询问转换模式

使用 `ask_user_question` 询问用户：
- 仅转换
- 仅审查
- 超级转换（转换 + 审查 + 修复）

#### 步骤 2：询问文件范围

使用 `ask_user_question` 询问用户：
- 全部文件组
- 用户自定义文件组

#### 步骤 3：扫描并分组文件

递归扫描 `winform` 目录，按规则分组：
- `*.designer.cs` + `*.cs` → 一组
- 只有 `*.cs` → 单独成组

**重要**：文件分组不跨文件夹，每个文件夹内的文件独立分组。

#### 步骤 4：创建任务列表

使用 `todo_write` 创建任务列表。
todo 任务列表需要逐个文件组开始执行，根据用户前两步选择的结果来组成任务列表

#### 步骤 5：执行任务

按文件组顺序，依次调用对应的 agent：

**转换**：
```markdown
task(
  subagent_type="transcode",
  description="转换文件组 {文件组名称}",
  prompt="请将以下文件组转换为 WPF 代码：
  - 文件组名称：{文件组名称}
  - 源文件：{源文件路径列表}
  - 目标目录：{目标目录路径}"
)
```

**审查**（仅审查或超级转换）：
```markdown
task(
  subagent_type="review",
  description="审查文件组 {文件组名称}",
  prompt="请审查以下文件组的 WPF 代码：
  - 文件组名称：{文件组名称}
  - 源文件：{源文件路径列表}
  - 目标文件：{目标文件路径列表}
  - 目标目录：{目标目录路径}"
)
```

**修复**（超级转换）：
```markdown
task(
  subagent_type="fix",
  description="修复文件组 {文件组名称}",
  prompt="请修复以下文件组的 WPF 代码：
  - 文件组名称：{文件组名称}
  - 源文件：{源文件路径列表}
  - 目标文件：{目标文件路径列表}
  - 转换日志：{转换日志路径}
  - 审查报告：{审查报告路径}
  - 目标目录：{目标目录路径}"
)
```

**重要**：必须等待当前步骤的 agent 完成后，才能执行下一步。

#### 步骤 6：验证结果

使用 `list_directory` 验证输出目录。

#### 步骤 7：报告进度

实时更新任务状态。

---

Todo 若用户进行其余场景操作，需要提醒他不支持

## 日志文件

- `{文件名}.trans.log.md` - 转换日志
- `{文件名}.review.log.md` - 审查报告
- `{文件名}.fix.log.md` - 修复日志

---

## 注意事项

1. 所有路径必须是绝对路径
2. 文件分组不跨文件夹
3. 每个文件组按顺序执行（转换 → 审查 → 修复）
4. 有需要确认的先与用户确认