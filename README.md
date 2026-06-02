# feishu-diary

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-orange)](https://claude.ai/code)

**中文** | English

---

## 功能简介

飞书语音日记自动归档系统 —— Claude Code Skill。

你说「整理日记」，AI 自动搜索飞书妙记中的语音日记，提取 Done List、灵感与感悟、关键词和摘要，写入多维表格并追加汇总文档。

### 核心流程

```
说「整理日记」
    ↓
搜索妙记 → 排重（跳过已归档）→ 获取逐字稿
    ↓
AI 提取四字段（Done List / 灵感与感悟 / 关键词 / 摘要）
    ↓
写入多维表格 + 两个汇总文档（最新在前）
```

### 设计理念

| 特性 | 说明 |
|------|------|
| **增量归档** | 三级排重（妙记链接精确 + 日期标题模糊 + 标题格式过滤），只写新条目 |
| **合并同类项** | Done List 中同一件事多次提及自动合并为一条 |
| **摘录原话** | 灵感与感悟尽量保留日记中的原话，不过度概括 |
| **同名保留** | 同一天多条日记各自独立，不覆盖 |
| **通用适配** | 首次使用询问用户配置，存入 Claude Code Memory |
| **格式统一** | 所有用户使用统一的输出格式模板（✅/💡 前缀、分号分隔摘要等） |

---

## 快速上手

### 前置条件

- **飞书 CLI**：如未安装，按[官方指南](https://open.feishu.cn/document/no_class/mcp-archive/feishu-cli-installation-guide.md)安装
- **飞书技能包**：`lark-minutes`、`lark-base`、`lark-doc`

```bash
npm install -g @larksuite/cli
npx -y skills add https://open.feishu.cn --skill -y
lark-cli config init --new
lark-cli auth login --recommend
```

### 安装 Skill

```bash
cd ~/.claude/skills
git clone https://github.com/cifen0327/feishu-diary.git
```

### 首次使用

在 Claude Code 中说「整理日记」，skill 会引导完成首次配置：

1. 提供日记多维表格链接
2. 提供 Done List 和灵感与感悟汇总文档链接
3. 选择日记标题格式（纯日期 / 日记+日期 / 自定义）
4. 系统自动验证并存入 Memory

之后每次说「整理日记」即可增量归档。

---

## 与 Claude Code 集成

Clone 到 `~/.claude/skills/feishu-diary` 后，Claude Code 自动加载。

### 触发方式

| 你说 | AI 行为 |
|------|---------|
| 「整理日记」「归档日记」「补充日记」 | 触发增量归档 |
| 「我上次日记里提到的那个面试怎么样了」 | 通过 Memory 查 Base 内容回复 |
| 「查一下我关于 XX 的感悟」 | 搜索 Base 关键词字段匹配 |

### 日常对话中的记忆调用

skill 将配置写入 Claude Code Memory（`feishu-diary-config`），日常对话中 AI 可自动引用已归档日记内容，无需每次重新说明存储位置。

---

## 写入内容

### 多维表格字段

| 字段 | 格式 | 说明 |
|------|------|------|
| 日期 | `2026-06-02 09:17:57` | 妙记创建时间 |
| 标题 | `6.2` | 按用户配置的格式生成 |
| 妙记链接 | 完整 URL | 有妙记链接则跳过逐字稿链接 |
| 逐字稿摘要 | `做A；做B；做C` | 一句话，分号分隔 |
| Done List | `✅ xxx` 每条一行 | 已完成动作，合并同类项 |
| 灵感与感悟 | `💡 xxx` 每条一行 | 摘录原话，保留口语风格 |
| 关键词 | `A, B, C` | 逗号分隔 3-5 个 |

### 汇总文档

两个文档均按日期倒序（最新在最前），Markdown 格式：

```markdown
## 6.2 · 2026-06-02
- Done List 条目一
- Done List 条目二

## 6.1 · 2026-06-01
- 条目一
```

---

## 项目结构

```
feishu-diary/
├── SKILL.md                       # Skill 主指令
├── README.md                      # 本文件
├── LICENSE                        # MIT 协议
└── references/
    ├── cli-install.md             # 飞书 CLI 安装指南
    ├── first-run-setup.md         # 首次配置引导流程
    └── output-format.md           # 输出格式规范
```

---

## FAQ

### 需要付费吗？

完全免费。依赖飞书多维表格和妙记的免费额度，不涉及任何付费 API。

### 我的数据安全吗？

所有数据存储在你自己的飞书多维表格和文档中，AI 仅在本地提取内容后写入，不上传任何数据到第三方。

### 可以多人使用吗？

可以。每人独立配置自己的 Base 和文档，skill 支持通用适配。

### 同一天有多条日记会怎样？

每条独立记录，不会相互覆盖。

---

## English

### What it does

A Claude Code skill that auto-archives Feishu voice diaries into a Base multi-dimensional table and two summary documents. Say "整理日记" and it:

1. Searches your Feishu Minutes for new diary recordings
2. Extracts Done List, insights, keywords, and summaries from voice transcripts
3. Writes to Base and appends summary docs in reverse chronological order
4. Handles duplicates, same-day multi-entries, and first-time configuration

### Quick Start

```bash
cd ~/.claude/skills
git clone https://github.com/cifen0327/feishu-diary.git
```

Then say "整理日记" in Claude Code. The skill guides through one-time setup.

### Requirements

- Feishu CLI (`@larksuite/cli`)
- Feishu skill packs: `lark-minutes`, `lark-base`, `lark-doc`
- A Feishu Base and two summary documents for storage

### License

MIT © 磁粉
