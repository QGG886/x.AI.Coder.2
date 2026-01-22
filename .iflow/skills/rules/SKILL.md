---
name: rules
description: "读取和管理 WinForms 到 WPF 转换规则"
---

# 规则管理技能

## 功能描述

读取和管理 WinForms 到 WPF 转换规则，支持智能规则选择和加载。

## 适用场景

- Agent 需要读取转换规则
- Agent 需要根据代码内容智能加载相关规则
- Agent 需要查看规则索引

## 输入参数

### 必需参数

| 参数名 | 类型 | 说明 | 示例 |
|--------|------|------|------|
| `description` | String | 规则需求描述 | "获取规则索引" 或 "查找命名空间转换规则" |

## 输出格式

### 成功输出 - 返回规则索引

```json
{
  "status": "success",
  "type": "index",
  "index": {
    "cs": {
      "common.md": "通用规则",
      "controls.md": "控件规则",
      "grid.md": "网格规则",
      "timer.md": "定时器规则",
      "special.md": "特殊规则"
    },
    "designer": {
      "layout.md": "布局规则",
      "controls.md": "控件规则",
      "events.md": "事件规则"
    }
  }
}
```

### 成功输出 - 返回具体规则内容

```json
{
  "status": "success",
  "type": "content",
  "file": "cs/common.md",
  "content": "# 通用规则\n..."
}
```

### 失败输出

```json
{
  "status": "failed",
  "error": "规则文件不存在"
}
```

## 规则加载逻辑

直接读取 `resources/rule_index.md` 获取规则索引，根据索引找到目标规则文件。

**加载策略**：
- 获取索引：读取 `resources/rule_index.md`，返回规则索引
- 查找规则：根据需求在规则索引中查找对应的规则文件，返回规则内容

## 使用方法

### 在 Agent 中调用

```markdown
## 执行操作

调用规则管理技能：

**技能调用**：
```
Skill: rules
参数:
  description: "获取规则索引"
```

**处理返回结果**：
- 检查 `status` 字段
- `type === 'index'`：返回规则索引
- `type === 'content'`：返回具体规则内容
- `status === 'failed'`：检查 `error` 字段处理错误
```

## 错误处理

| 错误类型 | 说明 | 处理方式 |
|---------|------|---------|
| `FILE_NOT_FOUND` | 规则文件不存在 | 返回错误信息，建议检查规则文件路径 |
| `READ_ERROR` | 读取失败 | 返回错误信息，建议检查文件权限 |
| `INVALID_DESCRIPTION` | 无效的描述 | 返回错误信息，建议提供更具体的描述 |

## 注意事项

- **路径处理**：所有规则文件路径都是相对于 `.iflow/skills/rules/resources/` 目录的
- **规则格式**：规则文件采用 Markdown 格式
- **规则索引**：详细索引和加载策略请参考 `resources/rule_index.md`
- **智能查找**：根据描述智能查找对应的规则文件

## 版本历史

- v1.0.0 (2026-01-18): 初始版本，支持规则加载功能
- v2.0.0 (2026-01-22): 简化为基于描述的智能查找方式