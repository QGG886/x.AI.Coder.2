---
name: log_analyzer
description: "分析日志，提取问题列表"
---

# 日志分析技能

## 功能描述

分析转换日志和审查日志，提取问题列表，汇总问题信息。

## 适用场景

- 分析 transcode 日志
- 分析 review 日志
- 提取问题列表
- 汇总问题统计

## 输入参数

### 必需参数

| 参数名 | 类型 | 说明 | 示例 |
|--------|------|------|------|
| `log_type` | String | 日志类型：`transcode`, `review`, `fix` | `review` |
| `log_content` | String | 日志内容 | 日志文件的完整内容 |

### 可选参数

| 参数名 | 类型 | 说明 | 默认值 |
|--------|------|------|--------|
| `extract_issues` | Boolean | 是否提取问题列表 | `true` |
| `extract_statistics` | Boolean | 是否提取统计信息 | `true` |
| `group_by_type` | Boolean | 是否按问题类型分组 | `true` |

## 输出格式

### 成功输出

```json
{
  "status": "success",
  "log_type": "review",
  "issues": [
    {
      "id": "1",
      "type": "错改",
      "severity": "high",
      "rule": "命名空间替换",
      "location": "line 5",
      "description": "命名空间未正确替换",
      "file": "Form1.xaml.cs",
      "source_snippet": "namespace xIR.UI",
      "target_snippet": "namespace xIR.UI",
      "suggested_fix": "namespace XUI"
    },
    {
      "id": "2",
      "type": "漏改",
      "severity": "medium",
      "rule": "属性转换",
      "location": "line 45",
      "description": "Visible 属性未转换为 Visibility",
      "file": "Form1.xaml.cs",
      "source_snippet": "this.button.Visible = true;",
      "target_snippet": "this.button.Visible = true;",
      "suggested_fix": "this.button.Visibility = Visibility.Visible;"
    }
  ],
  "statistics": {
    "total_issues": 2,
    "by_type": {
      "错改": 1,
      "漏改": 1,
      "多改": 0,
      "需确认": 0
    },
    "by_severity": {
      "high": 1,
      "medium": 1,
      "low": 0
    },
    "by_file": {
      "Form1.xaml.cs": 2
    }
  },
  "summary": {
    "total_files": 1,
    "total_issues": 2,
    "high_priority_issues": 1,
    "medium_priority_issues": 1,
    "low_priority_issues": 0
  }
}
```

### 失败输出

```json
{
  "status": "failed",
  "log_type": "review",
  "error": "日志解析失败",
  "error_details": "无法解析日志格式"
}
```

## 日志类型说明

| 日志类型 | 说明 | 文件扩展名 |
|---------|------|-----------|
| `transcode` | 转换日志 | `.trans.log.md` |
| `review` | 审查日志 | `.review.log.md` |
| `fix` | 修复日志 | `.fix.log.md` |

## 使用方法

### 在 Agent 中调用

```markdown
## 执行操作

调用日志分析技能：

**技能调用**：
```
Skill: log_analyzer
参数:
  log_type: review
  log_content: [从文件读取的日志内容]
  extract_issues: true
  extract_statistics: true
  group_by_type: true
```

**处理返回结果**：
- 检查 `status` 字段
- 获取 `issues` 数组（问题列表）
- 查看 `statistics`（统计信息）
- 查看 `summary`（汇总信息）
```

## 错误处理

### 常见错误类型

| 错误类型 | 说明 | 处理方式 |
|---------|------|---------|
| `LOG_PARSE_FAILED` | 日志解析失败 | 返回详细的错误信息 |
| `INVALID_LOG_TYPE` | 无效的日志类型 | 返回详细的错误信息 |
| `EMPTY_LOG` | 日志内容为空 | 返回警告，返回空结果 |

### 错误恢复策略

**日志解析失败**：
- 返回详细的错误信息
- 建议检查日志格式

**无效的日志类型**：
- 返回详细的错误信息
- 建议使用正确的日志类型

**日志内容为空**：
- 返回警告
- 返回空的结果结构

## 注意事项

1. **日志格式**：确保日志格式正确
2. **问题提取**：准确提取所有问题
3. **统计信息**：提供详细的统计信息
4. **问题分类**：按问题类型和严重程度分类
5. **文件关联**：记录问题关联的文件

## 版本历史

- v1.0.0 (2026-01-18): 初始版本，支持日志分析功能