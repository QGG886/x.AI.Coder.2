# 规则索引文件

本文件是规则加载的权威指南，所有 Agent 在加载规则时都应遵循本文件的指引。

---

## 规则文件结构

```
.iflow/skills/rules/resources/
├── rule_index.md           # 本文件：规则索引和加载指南
├── cs/                     # 后台代码转换规则
│   ├── common.md           # 通用属性规则（始终加载）
│   ├── controls.md         # 控件特定规则
│   ├── grid.md             # 表格规则
│   ├── timer.md            # 定时器规则
│   └── special.md          # 特殊场景规则
└── designer/               # 设计器代码转换规则
    ├── layout.md           # 布局规则（始终加载）
    ├── controls.md         # 控件替换规则
    └── events.md           # 事件规则
```

---

## 规则加载策略

### 后台代码（CS）
**始终加载**：`cs/common.md`

**按需加载**：
- CheckBox/Button/Label/ComboBox/TabControl → `cs/controls.md`
- Timer → `cs/timer.md`
- GridControl/GridView → `cs/grid.md`
- 窗体关闭事件/特殊方法调用/状态灯/分隔条 → `cs/special.md`

### 设计器代码（Designer）
**始终加载**：`designer/layout.md`

**按需加载**：
- 表格控件 → `designer/controls.md`
- 需要事件绑定的控件 → `designer/events.md`

### 规则完善
根据规则内容判断应该更新哪个模块化文件：
- Designer + XAML → `designer/` 下的相应文件
- CS + CS → `cs/` 下的相应文件

---

## 规则修改策略
- 修改代码 不要在代码段中出现详细命名空间，如需引用新的内容，请先using后，直接使用对应内容

## 规则内容索引

### CS 规则（后台代码）

| 文件 | 内容 |
|------|------|
| common.md | 命名空间替换、Visibility 属性转换、Color 类型转换、菜单单项属性转换 |
| controls.md | CheckBox、ComboBox、TabControl、账户选择、LayoutControl、状态灯 |
| grid.md | GridControl/GridView 属性、方法、事件转换 |
| timer.md | Timer 控件相关转换 |
| special.md | 窗体关闭事件、方法参数、状态灯、分隔条 |

### Designer 规则（设计器代码）

| 文件 | 内容 |
|------|------|
| layout.md | XAML 布局重构和控件放置规则 |
| controls.md | WinForms 控件到 WPF 控件的映射规则 |
| events.md | 设计器中的事件绑定规则 |

---

## 规则文件格式标准

```markdown
# [规则类别]

## [规则名称]

**规则内容**：
[详细说明]

**示例**：
```csharp
// WinForms
[代码]

// WPF
[代码]
```

**注意事项**：
[特殊情况说明]
```

---

## 重要说明

- **本文件是规则加载的权威指南**，所有 Agent 在加载规则时都应遵循本文件的指引
- **不要在各个 Agent 中重复编写规则加载逻辑**，只需引用本文件即可
- **规则文件路径都是相对于 `.iflow/skills/rules/resources/` 目录的**

## 版本

v5.0.0 (2026-01-20) - 全面简化和优化提示词