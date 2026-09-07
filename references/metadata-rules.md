# 元数据规则

本参考规定 Markdown 文档的元数据标记方式，是「多属性内容必用元数据」规则的实施细则。配合 [MarkdownQLite 渲染契约](render-contract.md) 使用；frontmatter 的渲染行为以该契约为准。

## 判据

文档需要向读者或机器陈述两个以上可独立列举的属性（版本、状态、日期、适用范围、来源、关联文档、负责人等）时，必用元数据标记；只有单一属性时写进正文即可。

属性要进元数据区，不散落在正文散文里。原因：元数据可被机器读取、可被检索、可被渲染器或工具直接消费，正文散文里的属性做不到。

## 首选形态：YAML frontmatter

- 文件以 `---` 起始和结束的 YAML 块为 frontmatter，置于文档最前。
- 字段键用英文 snake_case；值可用中文。与中文正文混排时遵循本技能的术语与排版规则。
- 一组相关属性收拢在同一个顶层键下，用嵌套分组表达，不拍平成 `scope_paths`、`source_skill` 一类的拼接键。
- 数组值用 YAML 列表；布尔与数值用 YAML 原生类型，不加引号。

示例骨架：

```yaml
---
title: 文档标题
type: design-spec
status: draft
version: "1.0"
date: 2026-09-07
updated: 2026-09-07
audience:
  - 开发者
scope:
  paths:
    - docs/
  exclude:
    - docs/samples/
source:
  skill: tech-doc-style-chinese-for-QL
related:
  - docs/design.md
---
```

## 推荐字段词汇

字段名不强制，渲染器接受任意键；下列词汇是推荐默认，同一项目内保持一致：

| 字段 | 含义 | 值形态 |
| --- | --- | --- |
| `title` | 文档标题 | 字符串；与首个 H1 一致或互补 |
| `type` | 文档类型 | 小写连字符词，如 `style-guide`、`design-spec`、`plan`、`report` |
| `status` | 当前状态 | 如 `draft`、`active`、`superseded`、`archived` |
| `version` | 文档自身版本 | 引号包裹的字符串，如 `"1.1"` |
| `date` | 创建日期 | `YYYY-MM-DD` |
| `updated` | 最近更新日期 | `YYYY-MM-DD` |
| `audience` | 目标读者 | 字符串列表 |
| `scope` | 适用范围 | 嵌套映射，如 `paths` / `exclude` |
| `source` | 事实或底稿来源 | 嵌套映射或字符串 |
| `related` | 关联文档 | 相对路径列表 |

项目自己的 `AGENTS.md` 或覆盖文件定义了项目字段词汇时，以项目为准。

## 值的可校验性

元数据只写可判真伪的值：

- 日期用 `YYYY-MM-DD`，不写「近期」「日前」。
- 版本用实际版本号，不写「最新」「新版」。
- 路径写实际相对路径，不写「相关文档若干」。
- 枚举状态用约定词表内的词，不自造同义词。

无法确认的属性标注 `pending` 或「待确认」，不编造。

## 机器可读边界

- frontmatter 属于机器可读内容：排版规则不作用于键名、路径、scheme 值。
- frontmatter 之后的正文规则不受影响；不要在 frontmatter 内混写正文段落。
- 排版批量替换工具不得触碰 frontmatter 区。
