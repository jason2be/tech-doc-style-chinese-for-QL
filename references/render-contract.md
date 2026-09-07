# MarkdownQLite 渲染契约（输出补丁）

> **权威源**：MarkdownQLite 仓库 `docs/render-spec.md`（v0.1，2026-09-07；从渲染代码逐条提取，每条标注代码出处与测试名）。
> **本文件角色**：面向写作 Agent 的便携能力目录。设计假设：Agent 不会自行推断应用能力——没写出来的能力等于不存在。因此每个能力都显式列出，并附可直接照抄的语法模板。
> **一致性规则**：本文件与权威源不一致时，以权威源为准，并按文末「同步」流程更新本文件。

## 怎么用本目录

1. 写作前通读一遍能力目录，确定文档要用的能力集合。
2. 用某个能力时，抄模板改内容，不自造变体。
3. 不确定某写法是否支持时，查第 12 节不支持清单；清单外的冷门写法换成清单内确认安全的能力。
4. 净化与链接安全边界（第 10 节）永远适用，无论文体。

## 1. 元数据（frontmatter）

识别条件：文件首行恰为 `---`，之后存在闭合行 `---`，两者之间至少一行非空内容；不支持 `...` 闭合。渲染为 Notion 风格属性区块，插在首个标题之后；不进正文、不进大纲。工作区搜索只索引 `title:` 与 `tags:`。

基础模板：

~~~markdown
---
title: 文档标题
type: design-spec
status: draft
version: "1.0"
date: 2026-09-07
updated: 2026-09-07
tags:
  - 标签一
  - 标签二
---
~~~

各值类型的能力（按匹配顺序，超出则转义为纯文本）：

| 值形态 | 渲染结果 | 模板 |
| --- | --- | --- |
| `tags:` / `tag:`（列表） | 彩色 chip，多个标签循环 7 种配色 | `tags:` 下用 `- 词` 列表，见上方模板 |
| `status:` | 单枚状态 chip | `status: draft` |
| `checklist:`（多于一项） | 属性清单列表 | `checklist:` 下用 `- 事项` 列表 |
| 恰为 `true` / `false` | 复选框（含勾选语义，非表单控件） | `optional: false` |
| `http(s)://`、`mailto:` 值 | 可点击链接 | `repo: https://github.com/x/y` |
| 其余标量 | 转义文本 | `owner: 团队名` |

嵌套分组：首层键显示为分组标题，其余层点分拼接为子键。多属性文档的完整模板：

~~~markdown
---
title: 高级元数据样例
type: sample
tags:
  - metadata
  - preview-lite
review:
  owner: 团队名
  required: true
  checklist:
    - frontmatter 顺序
    - 嵌套分组
navigation:
  previous: 02-preview-and-extensions.md
  related: 05-link-preview-acceptance.md
---
~~~

边界：对象数组、YAML anchor/alias、多行 block scalar 会被行解析器逐行降级，不要依赖其结构化语义；不要在 frontmatter 里写正文段落。

## 2. 结构与导航

~~~markdown
# 文档主题

[TOC]

## 安装 {#install}

跳转写法：见[安装](#install)。
~~~

- `[TOC]` 单独成行，生成全量标题目录（含引用块、列表内的标题）。
- 标题自动生成锚点 id；中文字符保留在 id 中。
- 标题行尾 `{#id}` 自定义锚点（`id` 限字母数字与 `_.:-`），从显示标题剥除。
- 同名标题自动 `-2` 递增消歧。
- 侧边栏大纲与目录跳转后目标块黄色高亮淡出。

## 3. 强调与符号

| 写法 | 渲染 | 模板 | 边界 |
| --- | --- | --- | --- |
| 删除线 | `<del>` | `~~废弃方案~~` | 单 `~` 是字面文本（`3~7` 区间安全），必须双 `~~` |
| 高亮 | `<mark>` | `==关键结论==` | 内容 ≥2 字符，首尾不含空白 |
| 中文着重号 | 自动 | `**这个结论**重要` | 强调内容含汉字时自动加着重号，纯西文保持斜体 |
| 紧邻汉字强调 | 自动补配 | `**中文**紧随文字` | CommonMark 漏配的场景渲染器已修补 |
| emoji | 表情 | `:rocket:` `:white_check_mark:` | 332 条精选目录；未知简码保持 `:name:` 字面 |
| 代码字面 | `<code>` | `` `:rocket:` `` | 行内代码内一切记号不解析 |

## 4. 列表与任务

~~~markdown
- [x] 已完成事项
- [ ] 待办事项
- [-] 被阻塞事项（等待依赖）
~~~

任务三态：`[x]` 完成、`[ ]` 未完成、`[-]` 阻塞（`[-]` 前缀会从正文剥除）。嵌套不超过三层，超过改用小标题。

## 5. 提示块（GFM 告警）

~~~markdown
> [!NOTE]
> 默认标题为类型词，按文档语言本地化。

> [!WARNING] 自定义标题
> marker 同行之后接标题文字，正文另起一行。
~~~

五个类型：`[!NOTE]`、`[!TIP]`、`[!IMPORTANT]`、`[!WARNING]`、`[!CAUTION]`。标题取 marker 同行后的文本，缺省时按文档语言显示本地化类型词。一块只承载一个要点。

## 6. 定义列表

~~~markdown
术语
: 定义内容，支持行内格式与链接。

另一术语
: 另一定义。
~~~

术语行（不以 `:` 开头的非空行）后跟一行或多行 `: ` 行即成定义列表；术语与定义都可含行内 Markdown。

## 7. 脚注（强能力）

基本模板：

~~~markdown
正文引用脚注[^1]，也可以用语义 id[^note-id]。

[^1]: 简单脚注。
[^note-id]: 支持**行内格式**与[链接](https://example.com)。
~~~

强能力逐项：

~~~markdown
[^multi]: 多段落脚注：第一段。

    第二段与嵌套列表（缩进 4 空格续写）：

    - 列表项，可含行内公式 $a^2$
    - 第二个列表项
~~~

- **多次引用**：同一 id 可在正文多处引用，每个引用点获得独立编号与独立回跳 `↩`。
- **回跳悬停预览**：悬停回跳箭头显示脚注上下文预览，按引用点前后文字分布从左/右/两侧渐隐。
- **编号**：按定义顺序自动编号；未定义的引用显示字面 `[^id]`，不会渲染成脚注。
- **字面场景**：行内代码与代码块内的 `[^id]` 不解析。

## 8. wikilink 与链接

wikilink 模板：

~~~markdown
- [[03-advanced-metadata]]      → 同目录存在的 md 文件 → 应用内打开
- [[锚点演示二]]                → 同文档标题（含后文定义的标题）→ 页内跳转
- [[不存在的目标]]              → 保持 [[…]] 字面原样
~~~

- 解析优先级：同文档标题（大小写/变音折叠、去空白匹配）→ 同目录 `name.md` 实存文件 → 保留字面。
- 不支持 `[[name|alias]]` 别名写法，`|` 会留在名字里。
- 名字含反引号（如 `` [[超立方体（`Q_n`）]] ``）可解析；名字含 `[`、`]` 或换行不保证。

外部与相对链接规则：

- 外部链接必须写显式 scheme：`https://example.com/page`、`mailto:a@b.com`。裸域名（`example.com/x`）**不成链**，保持纯文本。
- 自定义 scheme（`file/ftp/http/https/mailto` 之外）不保证成链。
- URL 尾部标点 `.,;:!?()[]{}` 会被剥除：结尾带括号的链接写成 `[标题](<https://…/a_(b)>)`（尖括号包裹）避免截断。
- 中文全角标点会终止 URL：URL 后紧跟 `。，` 等不会吞进链接。
- 相对链接与相对图片按文档所在目录解析，子目录相对路径可用；`../` 越界到文档目录之外的路径会被拒绝。
- 纯片段 `#id` 指向本文档锚点。

## 9. 数学公式

~~~markdown
行内：质能方程 $E = mc^2$。

块级：

$$
\int_{-\infty}^{\infty} e^{-x^2}\,dx = \sqrt{\pi}
$$
~~~

- 分隔符：行内 `$…$` 与 `\(…\)`；块级独立成段的 `$$…$$` 与 `\[…\]`（可多行）。
- `$…$` 守卫：内容非空且非纯数字——`$5$`、价格 `$19.99` 保持文本，不会误判成公式。
- 受支持命令族：分数（`\frac`）、根号（含 `\sqrt[3]{x}` n 次根）、上下标、矩阵环境（`matrix/pmatrix/bmatrix/vmatrix/Bmatrix/cases/array/aligned/align`，`&` 分列 `\\` 换行）、大算符上下限（`\sum`、`\lim`、`\int` 等）、数学字体（`\mathbb`、`\mathcal` 等 mathvariant）、`\overline/\underline/\boxed/\stackrel/\overset/\underset/\text`、常用函数名、一组符号与重音。
- 未知 `\command` 不渲染为错误图形：整段公式降级为转义源文并保留 `data-latex`，点击可复制原始 LaTeX。写作含义：只用上面列出的命令族；冷门命令先降级预期再使用。
- 渲染目标是原生 MathML，无外部依赖，离线可用。

## 10. Raw HTML 与净化边界

默认策略为白名单净化（`.safeSubset`）。写作时可安全使用的 HTML 能力：

~~~markdown
<details>
<summary>展开说明</summary>

折叠内容，Markdown 行内格式可用。

</details>

<a id="appendix"></a>

附录内容，可用 [附录](#appendix) 跳转。
~~~

- 保留标签：`p div h1-h6 ul ol li blockquote pre code span em strong b i u s del sub sup a img table thead tbody tr th td br hr dl dt dd details summary`。
- 保留属性仅限：`a[href,title]`、`img[src,alt,title,width,height]`、`th/td[colspan,rowspan,align]`、`code/pre[class]`；其余属性（含 `id`、`class`、`style`）剥除。
- 锚点的唯一 HTML 通道是纯空锚点 `<a id="x"></a>`；正文章节锚点优先用 `{#id}` 语法。
- HTML 注释 `<!-- … -->` 被剥除不显示；不要用它承载可见内容或结构信息。
- 白名单外标签（`<script>`、`<iframe>`、`<style>`、`<form>` 等）转义为文本显示，永不执行。
- `javascript:`、`vbscript:` 链接降级为纯文本；`data:` 仅允许作为图片 `src`。
- 用户可切换 `.escapedSource` 策略（一切 raw HTML 显示为转义源码）；打印管线固定按转义源码处理——不要让文档表达力依赖 raw HTML。

## 11. 图片

~~~markdown
![架构图](assets/architecture.png)
![截图](shots/2026-09-07.png)
![远程图](https://example.com/diagram.png)
~~~

- 相对路径按文档目录解析；`mql-img` 只读文档目录内的图片文件，目录穿越与 symlink 逃逸一律拒绝。
- 远程图片（http/https）可显示；远程脚本、远程 CSS、远程字体一律不加载。
- 图片写必填的 alt 描述；宽高可用 HTML 属性 `width`/`height`。

## 12. 不支持 / 谨慎使用清单

| 输入 | 行为 | 替代写法 |
| --- | --- | --- |
| 裸域名 URL | 不成链，纯文本 | 写全 `https://…` |
| 单 `~` 删除线 | 字面文本 | 用 `~~…~~` |
| `[[name|alias]]` | `|` 留在名字里 | 用 wikilink + 正文说明 |
| Mermaid / mindmap 围栏 | 代码块原文，不渲染图 | 文字描述 + 代码块 |
| `@directive` 块指令 | 不解析，普通文本 | 不使用 |
| frontmatter 对象数组 / block scalar | 逐行降级 | 平铺键或 `- ` 列表 |
| HTML `id`/`class`/`style` 属性 | 剥除 | `{#id}` 锚点、语义标签 |
| 正文 `data:` 链接 | 拒绝 | 不使用 |
| 目录穿越路径（`../` 出文档目录） | 拒绝 | 文件放文档目录内 |
| 未知 emoji 简码 | 字面保留 | 换目录内简码或直接输入字符 |
| 未知 LaTeX 命令 | 公式降级为源文 | 只用第 9 节命令族 |
| 远程脚本 / CSS / 字体 | CSP 阻止 | 不依赖 |

## 同步

- 权威源（MarkdownQLite `docs/render-spec.md`）更新后，同步本文件对应章节，并更新本文件头部的权威源版本与日期。
- 本文件在两个仓库各有一份副本（`tech-doc-style-chinese-for-QL` 与 `markdownqlite-md-writing-guide`），同步时两份一起更新。
- 代码渲染行为变化时：先改权威源（以代码为准），再同步本文件，最后判断 SKILL.md 输出补丁是否需要跟进。
