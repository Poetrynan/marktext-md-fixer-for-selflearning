# 🛠️ Claude Code Skills for Markdown Formatting

> 🎯 **让学习笔记更整洁，让公式正确显示，让标题不再混乱！**

[🇬🇧 English](./README.md) | [🇨🇳 中文文档](./README_CN.md)

---

## 🤔 为什么需要这些 Skills？

### 📖 背景故事

作为学生，我经常需要：
1. **整理学习笔记**：把课堂内容、AI 解释、自己的理解整理成 Markdown 文件
2. **使用 MarkText 查看**：MarkText 是一个优秀的 Markdown 编辑器，但对 LaTeX 公式有特殊要求
3. **向 AI 追问知识点**：把不理解的内容发给 AI，然后把解释粘贴回笔记

### 😩 遇到的问题

**问题 1：公式显示异常**
```
# 从 AI 复制的公式
\(E = mc^2\)        # MarkText 不支持这种格式
\[
\int_0^1 f(x) dx    # MarkText 不支持这种格式
\]
```

**问题 2：标题序号冲突**
```markdown
## 一、什么是强化学习？    # 原始内容

# 粘贴 AI 解释后...
## 一、核心概念            # 冲突！又从"一"开始了
## 二、基本原理            # 与原始"二"冲突
```

**问题 3：格式混乱**
- `$$` 块前面有缩进，MarkText 不渲染
- `$$` 结束标记后面跟着文字
- `<br>` 标签在表格中显示为原始文本

### 💡 解决方案

我创建了两个 Claude Code Skills：

1. **🔧 MarkText LaTeX Fix**：自动修复 LaTeX 公式格式
2. **📝 Markdown Heading Fix**：自动修复标题序号冲突

---

## 🚀 快速开始

### 安装方法

#### 方法一：克隆仓库（推荐）
```bash
git clone https://github.com/Poetrynan/claude-code-skills.git
cd claude-code-skills
```

#### 方法二：手动复制
将 `skills/` 目录下的文件复制到你的 Claude Code 插件目录：
```bash
~/.claude/plugins/marketplaces/claude-plugins-official/plugins/
```

### 使用方法

#### 🔧 修复公式格式
当你的 Markdown 公式在 MarkText 中无法显示时：

```
你：修复公式格式
Claude：（自动执行 LaTeX 修复流程）
```

#### 📝 修复标题序号
当粘贴 AI 解释导致标题冲突时：

```
你：排版标题
Claude：（自动执行标题修复流程）
```

---

## 📚 详细文档

### 🔧 [MarkText LaTeX Fix](./skills/marktext-latex-fix/)

**功能：**
- 🔄 `\(...\)` → `$...$`（行内公式）
- 🔄 `\[...\]` → `$$...$$`（块级公式）
- ✅ 确保 `$$` 块前后有空行
- ✅ 移除 `$$` 块的额外缩进
- ✅ 修复 `$$` 结束标记与文字同行的问题
- ✅ 替换 `<br>` 标签

**触发关键词：**
- "公式不显示"、"LaTeX"、"数学公式"
- "修复公式格式"、"MarkText"

### 📝 [Markdown Heading Fix](./skills/md-heading-fix/)

**功能：**
- 🔄 解决标题序号冲突
- 🔄 修复标题层级不匹配
- ✅ 更新子节号以匹配父节
- ✅ 移除粘贴内容的额外缩进
- ✅ 将 AI 解释标题转换为正确的子节

**触发关键词：**
- "标题冲突"、"排版标题"
- "重新编号"、"标题序号"

---

## 🎯 使用场景

| 场景 | 使用的 Skill | 触发关键词 |
|------|-------------|-----------|
| 📐 公式在 MarkText 中不显示 | LaTeX Fix | "公式不显示"、"LaTeX" |
| 📝 粘贴 AI 解释后标题混乱 | Heading Fix | "标题冲突"、"排版标题" |
| 🔢 标题序号重复或不连续 | Heading Fix | "重新编号"、"标题序号" |
| 📊 表格中的 `<br>` 标签显示异常 | LaTeX Fix | "br标签"、"HTML标签" |

---

## 📖 使用示例

### 示例 1：修复公式格式

**修复前：**
```markdown
\(E = mc^2\)

\[
\int_0^1 f(x) dx
\]
```

**修复后：**
```markdown
$E = mc^2$

$$
\int_0^1 f(x) dx
$$
```

### 示例 2：修复标题冲突

**修复前：**
```markdown
## 一、什么是强化学习？

## 一、核心概念  ← 冲突！
## 二、基本原理  ← 与原始二冲突
```

**修复后：**
```markdown
## 一、什么是强化学习？

## 二、核心概念  ← 已修复
## 三、基本原理  ← 已修复
```

### 示例 3：修复子节号

**修复前：**
```markdown
## 五、回报与折扣因子
### 3.1 问题  ← 错误
### 3.2 公式  ← 错误
```

**修复后：**
```markdown
## 五、回报与折扣因子
### 5.1 问题  ← 正确
### 5.2 公式  ← 正确
```

---

## 🛠️ 技术细节

### LaTeX Fix 技能

**修复流程：**
1. 替换公式分隔符（`\(` → `$`，`\)` → `$`）
2. 替换块级公式分隔符（`\[` → `$$`，`\]` → `$$`）
3. 确保 `$$` 前后有空行
4. 移除 `$$` 块的额外缩进
5. 修复 `$$` 结束标记与文字同行的问题
6. 替换 `<br>` 标签

### Heading Fix 技能

**修复流程：**
1. 全面了解文件结构
2. 识别 AI 解释块
3. 分析冲突类型
4. 制定重编号方案
5. 执行编辑
6. 修复子节号
7. 修复缩进问题
8. 验证结果

---

## 📁 文件结构

```
claude-code-skills/
├── LICENSE                    # 📄 MIT 开源许可证
├── README.md                  # 📖 英文说明文档
├── README_CN.md               # 📖 中文说明文档
└── skills/
    ├── marktext-latex-fix/
    │   └── SKILL.md           # ⚙️ LaTeX 修复 skill 定义
    └── md-heading-fix/
        └── SKILL.md           # ⚙️ 标题修复 skill 定义
```

---

## 🤝 贡献

欢迎贡献！请随时提交 Issue 或 Pull Request。

**贡献方式：**
1. 🐛 报告 Bug
2. 💡 提出新功能建议
3. 📝 改进文档
4. 🔧 提交代码修复

---

## 📄 许可证

MIT License - 欢迎任何人使用、修改和分发这些技能。

---

## 👨‍💻 作者

**Poetrynan** - [GitHub](https://github.com/Poetrynan)

---

## 🙏 致谢

感谢 Claude Code 提供的自定义 skill 功能，让这些工具成为可能！

---

## 📞 联系方式

如果你有任何问题或建议，欢迎通过以下方式联系：

- 📧 GitHub Issues
- 💬 GitHub Discussions

---

**💡 提示**：如果你有新的 skill 需求，欢迎在 GitHub 上提出 Issue！
