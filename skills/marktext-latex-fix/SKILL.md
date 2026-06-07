---
name: fix-latex
description: This skill should be used when the user mentions "公式不显示", "公式显示不对", "LaTeX", "数学公式", "MarkText", "md公式", "markdown公式", "br标签", or asks to fix math formulas in .md files for MarkText rendering.
---

# MarkText LaTeX 公式修复

修复 `.md` 文件中的 LaTeX 公式格式，使其在 MarkText 中正确渲染。

**一句话经验总结：在 MarkText 里写数学公式 = 键盘符号 + `\begin{aligned}` + `\text{中文}`。**

## 触发条件

当用户提到以下关键词时自动触发：
- "公式不显示"、"公式显示不对"、"LaTeX"、"数学公式"
- "MarkText"、"md公式"、"markdown公式"
- "br标签"、"HTML标签"
- 涉及 `.md` 文件中的数学公式问题

---

## 一、终极黄金法则

**键盘上有的符号，绝对不要写 LaTeX 命令。**

| 你想要的效果 | ❌ 千万不要写（MarkText 可能认不出） | ✅ 绝对要写（MarkText 认死了） |
|-------------|-----------------------------------|------------------------------|
| 方括号 `[ ]` | `\lbrack` 和 `\rbrack` | 直接打键盘上的 `[` 和 `]` |
| 条件概率竖线 `\|` | `\mid`（在某些情况下可以） | 直接打键盘上的 `\|` |
| 圆括号 `( )` | `\(` 和 `\)`（作为命令） | 直接打键盘上的 `(` 和 `)` |
| 花括号 `{ }` | `\lbrace` 和 `\rbrace` | 直接打键盘上的 `{` 和 `}`（需要转义为 `\{` 和 `\}`） |

---

## 二、方程组终极写法

**忘记 `\begin{cases}`，拥抱 `\begin{aligned}`。**

**反例（MarkText 不识别）：**
```latex
\begin{cases}
x = 0.8x + 0.6y \\
y = 0.2x + 0.4y
\end{cases}
```

**正例（百分百能显示）：**
```latex
$$
\left\{
\begin{aligned}
x &= 0.8x + 0.6y \\
y &= 0.2x + 0.4y
\end{aligned}
\right.
$$
```

**原理：** MarkText 的数学引擎（KaTeX）对 `\begin{cases}` 支持不完善，但 `\begin{aligned}` 是最基础的对齐环境，稳如泰山。

---

## 三、文本与公式混排写法

**中文注释不要写在公式块外面，要用 `\text{}` 包起来。**

**反例（会导致渲染断开/乱码）：**
```latex
$$
\sum_{j} P_{ij} = 1
$ (从状态i出发，去所有状态的概率和 = 1)
```

**正例（完美显示）：**
```latex
$$
\sum_{j} P_{ij} = 1 \quad \text{(从状态i出发，去所有状态的概率和 = 1)}
$$
```

**原理：** 把注释放进 `\text{}` 里，能保证注释和公式作为一个整体，不会因为换行或空格导致渲染引擎"看懵"。

---

## 四、反斜杠 `\` 和空格的规则

**核心原则：** `\ `（反斜杠+空格）= "插入一个空格"的命令

**常见错误：** `\0`、`\,`、`\20` 等都是**未定义的命令**，会导致渲染失败！

```markdown
# ❌ 错误写法（\ 后面跟了 0，未定义命令）
$$
[ 0.80,\ 0.20 ]
$$

# ✅ 正确写法（\ 后面跟空格，或者直接不加 \）
$$
[0.80, 0.20]
$$

# ✅ 也可以（明确指定空格）
$$
[0.80, \ 0.20]
$$
```

**最安全的写法：** 直接打 `[0.80, 0.20]`，让 KaTeX 自动处理间距，根本不需要手动加 `\`。

**如果你非要加显式的空格，记住格式：** `\ `（反斜杠+空格）

---

## 五、修复步骤

### Step 1: 替换行内公式分隔符

使用 Edit 工具的 `replace_all` 选项：
- `\(` → `$`
- `\)` → `$`

### Step 2: 替换块级公式分隔符

使用 Edit 工具的 `replace_all` 选项：
- `\[` → `$$`
- `\]` → `$$`

### Step 3: 替换 LaTeX 方括号命令

使用 Edit 工具的 `replace_all` 选项：
- `\lbrack` → `[`
- `\rbrack` → `]`

### Step 4: 替换 `\begin{cases}` 为 `\begin{aligned}`

使用 Edit 工具替换：
```
\begin{cases} → \left\{ \begin{aligned}
\end{cases} → \end{aligned} \right.
```

### Step 5: 确保 `$$` 前后有空行

使用 Grep 工具查找所有 `$$` 行：
```
pattern: ^\$\$
output_mode: content
-B: 1
-A: 1
```

### Step 6: 移除 `$$` 块的缩进

使用 Grep 查找有缩进的 `$$` 行：
```
pattern: ^ +\$\$
output_mode: content
-n: true
```

### Step 7: 修复 `$$` 结束标记与文字同行的问题

使用 Grep 查找 `$$` 后面有文字的行：
```
pattern: \$\$.+
output_mode: content
-n: true
```

### Step 8: 修复未闭合的 Markdown 标记

使用 Bash 检查未闭合的 `**` 标记：
```bash
grep -n '^\*\*[^*]' filename.md | grep -v '\*\*$'
```

### Step 9: 修复 `<br>` 标签

使用 Grep 工具查找所有 `<br>` 标签：
```
pattern: <br>
output_mode: content
-n: true
```

### Step 10: 修复 LaTeX 公式中的中文字符

使用 Grep 查找包含中文字符的 LaTeX 公式：
```
pattern: \\text\{[^}]*[一-鿿]
output_mode: content
-n: true
```

**修复方案：**
- **块级公式**：将包含中文的公式转换为 Markdown 表格
- **行内公式**：将中文文本移到公式外部

### Step 11: 修复表格中 LaTeX 公式的 `|` 字符

使用 Bash 查找表格中包含 `|` 的 LaTeX 公式：
```bash
grep -n '|.*\$[^$]*|[^$]*\$.*|' filename.md | grep -v '\\mid'
```

### Step 12: 修复缺失的 `$` 分隔符

检查表格中是否有纯文本的变量（如 `S`、`A`、`P`、`R`、`γ`）没有 `$` 分隔符。

---

## 六、完整修复流程

1. **Step 1-2**：替换公式分隔符（`\(...\)` → `$...$`，`\[...\]` → `$$...$$`）
2. **Step 3**：替换 LaTeX 方括号命令（`\lbrack` → `[`，`\rbrack` → `]`）
3. **Step 4**：替换 `\begin{cases}` 为 `\begin{aligned}`
4. **Step 5**：确保 `$$` 前后有空行
5. **Step 6**：移除 `$$` 块的缩进（代码块中的缩进除外）
6. **Step 7**：修复 `$$` 结束标记与文字同行的问题
7. **Step 8**：修复未闭合的 Markdown 标记
8. **Step 9**：修复 `<br>` 标签
9. **Step 10**：修复 LaTeX 公式中的中文字符
10. **Step 11**：修复表格中 LaTeX 公式的 `|` 字符
11. **Step 12**：修复缺失的 `$` 分隔符

---

## 七、注意事项

- 不要修改公式内容本身（如 `\begin{pmatrix}` 等 LaTeX 语法）
- 不要修改非公式的文本内容
- 保留原有的 markdown 格式（标题、列表、表格等）
- 如果文件中已有正确的 `$...$` 格式，不要重复替换
- **表格中的 `|` 符号会与 LaTeX 的 `\mid` 冲突**，需要特别处理
- `<br>` 标签在 MarkText 中可能显示为原始文本，需要替换为其他分隔符
- **代码块中的缩进是正常的**，不需要修复
- **中文字符在 `\text{}` 中可以正常渲染**
- **未闭合的 Markdown 标记会导致后续内容无法正确渲染**，需要检查并修复
- **粘贴内容可能会丢失 `$` 分隔符**，需要检查并修复缺失的分隔符
- **在 PowerShell 中不要用 sed 处理 `\` 转义**，sed 会丢失反斜杠

---

## ⚠️ 重要：工具选择

### ❌ 不要用 sed 处理 LaTeX 的 `\` 转义

在 PowerShell 环境中，`sed` 的 `\` 转义会被 shell 吃掉，导致：
- `\mid` → `mid`（丢失反斜杠）
- `\gamma` → `gamma`（丢失反斜杠）
- `\text{...}` → `text{...}`（丢失反斜杠）

### ✅ 正确做法：用 Edit 工具

对于需要处理 `\` 转义的 LaTeX 修改，**必须使用 Edit 工具**：

```
Edit 工具：
  old_string: "$P(s' mid s,a)$"
  new_string: "$P(s' \\mid s,a)$"
  replace_all: false
```

**为什么 Edit 工具可以？**
- Edit 工具直接处理字符串，不经过 shell 解析
- `\` 不会被吃掉，能正确保留

### 修复 LaTeX 的推荐流程

1. **简单替换**（无 `\` 转义）：可以用 sed
   ```bash
   sed -i 's/old/new/g' file.md  # ✅ 可以
   ```

2. **涉及 `\` 转义**（如 `\mid`、`\gamma`、`\text`）：必须用 Edit 工具
   ```
   Edit 工具：old → new  # ✅ 可以
   sed 's/\\mid/mid/g'   # ❌ PowerShell 会吃掉 \
   ```

3. **批量替换多个 `\` 转义**：逐个用 Edit 工具处理

### ✅ 最佳实践：使用 Python 脚本进行复杂格式化

当遇到大篇幅多行文本重构、严重压缩的单行文本、或者有极多 LaTeX 反斜杠（`\`）需要替换时，逐个使用编辑工具或命令行极易出错。**此时推荐编写临时 Python 脚本进行处理**：
- 使用 `open(file_path, 'w', encoding='utf-8')` 确保中文字符集正常写入。
- 在脚本中通过 `r"""..."""` 原始多行字符串容纳并替换公式，以防 `\` 转义字符在终端被吞。
- 运行脚本完成一次性重构后，再将临时脚本从工作区中清理掉。
