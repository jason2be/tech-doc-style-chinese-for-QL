---
title: 上游跟踪与合并
type: upstream-manifest
status: active
date: 2026-09-07
upstream:
  skill: tech-doc-style-chinese
  author: Fenng
  license: MIT
  snapshot_date: 2026-09-07
---

# UPSTREAM.md — 上游跟踪与合并

本仓库是 `tech-doc-style-chinese` 的衍生分支。本文件记录上游快照与合并流程，使分支与上游的增量始终可辨、可再合并。

## 上游信息

- 上游技能：`tech-doc-style-chinese`（中文技术文档与产品文案规范）
- 上游作者：Fenng（GitHub：@Fenng）
- 上游许可：MIT License（见 LICENSE 上半部分）
- 上游公开仓库：https://github.com/Fenng/tech-doc-style-chinese
- 上游本机位置：`~/.agents/skills/tech-doc-style-chinese/`（本机合并流程约定）
- 快照日期：2026-09-07

## 分支增量（本仓库对上游的改动）

| 文件 | 操作 | 内容 |
| --- | --- | --- |
| `SKILL.md` | 改写 | 分支身份（name/description）、与上游关系、规则优先级插入渲染契约一级、新增「MarkdownQLite 渲染契约输出补丁」章节、编辑流程与最终检查加入契约项、参考资料路由加入渲染契约 |
| `references/render-contract.md` | 新建 | 忠实于 MarkdownQLite 渲染代码的渲染契约与输出补丁 |
| `references/metadata-rules.md` | 新建 | frontmatter 元数据规则细则 |
| `LICENSE` | 追加 | 衍生版权行 |
| `README.md` | 追加+改写 | 分支说明与安装/结构文档；本表只列分支动机，改动以 git 历史为准 |
| `UPSTREAM.md` | 新建 | 本文件 |
| `agents.md` | 新建 | 本仓库 Agent 约定 |
| `.gitignore` | 新增 | 忽略 `.DS_Store` |

上游文件（`NoCode-Skill.md`、`agents/`、`scripts/`、`tests/`、`references/` 既有四个文件）保持原样，未做修改；`.github/` 未随导入快照带入本仓库。`README.md` 与 `LICENSE` 在上游文本基础上追加分支内容（见上表）；`.gitignore` 为本分支新增。

## 上游快照哈希（2026-09-07）

检测上游更新：对本表各文件重算 SHA-256，与下表不一致即上游已变动，触发合并流程。

| 文件 | SHA-256 |
| --- | --- |
| `SKILL.md` | `f264e644f97a73ad572cc123d5b097ee0c8885710c73c2bcbf7c65fd4a4f6038` |
| `references/api-status-copy.md` | `2d8a429db092dc9679fce6b2b44e14b11ab1c861764c91a4eaee203fcebbe0cc` |
| `references/controlled-technical-chinese.md` | `24306b6cb882916f2fdff543ef459f65111310d32a2ceca4b38e12487ed03449` |
| `references/project-overrides-example.md` | `f3c28871305dcbdca0f450cf276831cc74d8cd192a9cf63df2fbff43b10d02b1` |
| `references/terminology-and-typography.md` | `ea826e5319543d3002d9e0e977098be6aa21abeb97d3325f5bd18dc995ba19dc` |

注：上游非 git 仓库，无法记录 commit，哈希清单即快照凭证。`agents/`、`scripts/`、`tests/` 未逐文件记录，合并时按目录整体对比。

## 上游更新合并流程

1. 对上游各文件重算哈希，与本文件快照表对比，列出变动文件。
2. 对变动文件做三方对比：上游新版、上游旧版（快照）、本分支版本。上游旧版以本文件哈希对应的本机副本为准；若本机已被更新，从本仓库 git 历史的初始导入提交恢复。
3. 逐文件合并：上游写作规则直接吸收；与渲染契约冲突的规则，按 SKILL.md 规则优先级处理（事实与机器可读内容优先，渲染约束次之，其余以上游为准）。
4. 合并后更新本文件快照哈希与快照日期，并在本文件末尾「合并记录」追加一行。
5. 若渲染行为本身变化（渲染器代码改动），先更新 `references/render-contract.md`（以 MarkdownQLite 代码为准），再判断是否影响输出补丁。

## 合并记录

- 2026-09-07：建立分支，快照哈希如上表，无上游合并。
