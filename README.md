# x.AI.Coder.2

## 项目介绍

x.AI.Coder.2 是一个基于 iFlow CLI 的智能代码转换工具，专门用于将 WinForms 项目自动转换为 WPF 项目。

本项目提供 5 个智能 Agent，能够完成从规则查询、代码转换、代码审查到自动修复的完整工作流：

- **rulesFQA** - 查询 WinForms 到 WPF 的转换规则
- **rule_refinement** - 通过对比代码自动总结并完善转换规则
- **transcode** - 将单个文件组的 WinForms 代码转换为 WPF 代码
- **review** - 审查已转换的 WPF 代码，识别转换问题
- **fix** - 基于转换日志和审查报告自动修复 WPF 代码

## 环境要求

- **操作系统**: Linux / macOS / Windows
- **Python**: 3.8+
- **Git**: 2.0+
- **iFlow CLI**: 最新版本

## iFlow CLI 安装

### 方法 1: 使用 npm 安装

```bash
npm install -g iflow-cli
```

### 方法 2: 使用 pip 安装

```bash
pip install iflow-cli
```

### 方法 3: 从源码安装

```bash
git clone https://github.com/iFlow-CLI/iflow-cli.git
cd iflow-cli
npm install
npm link
```

### 验证安装

```bash
iflow --version
```

## 使用方法

### 1. 克隆项目

```bash
git clone https://github.com/QGG886/x.AI.Coder.2.git
cd x.AI.Coder.2
```

### 2. 准备 WinForms 源代码

将需要转换的 WinForms 项目文件放入 `winform/` 目录。

### 3. 启动 iFlow CLI

在项目根目录下运行：

```bash
iflow
```

### 4. 使用 Agent 功能

#### 查询转换规则

```
WinForms 的 LayoutVisibility.Never 在 WPF 中应该怎么转换？
```

系统会自动调用 `rulesFQA` Agent 查询并返回转换规则。

#### 开始代码转换

```
开始转换
```

系统会引导您完成以下步骤：

1. **选择转换模式**：
   - 仅转换
   - 仅审查
   - 超级转换（转换 + 审查 + 修复）

2. **选择文件范围**：
   - 全部文件组
   - 用户自定义文件组

3. **自动执行转换流程**：
   - 扫描 `winform/` 目录并分组文件
   - 按顺序执行转换、审查、修复
   - 生成转换日志和审查报告

#### 合并转换规则

```
帮我对比 winform 和 wpf 文件夹，总结并完善转换规则
```

系统会调用 `rule_refinement` Agent 自动分析和优化转换规则。

## 使用案例

### 案例 1: 转换单个 WinForms 窗体

假设您有一个 WinForms 窗体 `FrmCFETSFAKQuote` 需要转换为 WPF：

1. 将以下文件放入 `winform/CFETSXBond/` 目录：
   - `FrmCFETSFAKQuote.cs`
   - `FrmCFETSFAKQuote.Designer.cs`
   - `FrmCFETSFAKQuote.resx`

2. 启动 iFlow CLI 并输入：
   ```
   开始转换
   ```

3. 选择"超级转换"和"全部文件组"

4. 系统自动完成转换，在 `wpf/CFETSXBond/` 目录生成：
   - `FrmCFETSFAKQuote.xaml` - WPF 界面定义
   - `FrmCFETSFAKQuote.xaml.cs` - WPF 代码逻辑
   - `FrmCFETSFAKQuote.trans.log.md` - 转换日志
   - `FrmCFETSFAKQuote.review.log.md` - 审查报告
   - `FrmCFETSFAKQuote.fix.log.md` - 修复日志

### 案例 2: 查询特定控件的转换规则

如果您想知道 WinForms 的 `DataGridView` 如何转换为 WPF：

```
WinForms 的 DataGridView 在 WPF 中应该怎么转换？
```

系统会返回详细的转换规则和示例代码。

### 案例 3: 批量转换整个项目

1. 将整个 WinForms 项目的所有窗体放入 `winform/` 目录

2. 启动 iFlow CLI 并输入：
   ```
   开始转换
   ```

3. 选择"超级转换"和"全部文件组"

4. 系统会自动：
   - 扫描所有 WinForms 文件
   - 按文件夹分组
   - 依次转换每个文件组
   - 执行代码审查
   - 自动修复发现的问题
   - 生成完整的转换报告

## 项目结构

```
x.AI.Coder.2/
├── AGENTS.md                      # Agent 使用指南
├── README.md                      # 项目说明
├── winform/                       # WinForms 源代码目录
│   └── CFETSXBond/
│       ├── FrmCFETSFAKQuote.cs
│       ├── FrmCFETSFAKQuote.Designer.cs
│       └── ...
├── wpf/                           # WPF 目标代码目录（自动生成）
│   └── CFETSXBond/
│       ├── FrmCFETSFAKQuote.xaml
│       ├── FrmCFETSFAKQuote.xaml.cs
│       └── ...
└── .iflow/                        # iFlow 配置和资源
    ├── agents/                    # Agent 定义
    │   ├── transcode.md
    │   ├── review.md
    │   ├── fix.md
    │   ├── rule_refinement.md
    │   └── rulesFQA.md
    └── skills/                    # 技能定义
        └── rules/                 # 转换规则
            └── resources/
                ├── rule_index.md
                ├── cs/            # C# 代码转换规则
                └── designer/      # Designer 文件转换规则
```

## 转换规则

项目内置了完整的 WinForms 到 WPF 转换规则，涵盖：

- **控件转换**: Button, TextBox, Label, ComboBox, DataGridView 等
- **布局转换**: TableLayoutPanel, FlowLayoutPanel, SplitContainer 等
- **事件处理**: Click, Load, FormClosing 等
- **特殊语法**: Timer, BackgroundWorker, Invoke 等

详细的规则文档请查看 `.iflow/skills/rules/resources/` 目录。

## 许可证

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request！
