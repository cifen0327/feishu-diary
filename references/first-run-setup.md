# 首次配置引导

当 Claude Code Memory 中不存在 `feishu-diary-config` 时，按以下流程完成首次配置。

## 配置流程

### 1. 欢迎与说明

向用户说明：
- 这个 skill 会将飞书妙记的语音日记自动归档到多维表格和汇总文档
- 需要设置一些基本信息，只需设置一次，后续存入 Memory 自动读取

### 2. 询问 Base 和汇总文档

**问题 1**：「请提供你的日记归档多维表格的链接或 Base Token。」

获取后立即用 `lark-cli base +field-list --base-token <token> --table-id <table_id> --as user` 读取字段结构，确认表存在且有权限访问。

**问题 2**：「你有两个汇总文档吗？请分别提供 Done List 汇总和灵感与感悟汇总的链接。」

如果用户只有一个汇总文档（两者合在一起），也支持，只需一个链接。

### 3. 询问日记标题格式

向用户展示：

```
你的妙记中，日记的标题格式是什么？
  a) 纯日期数字 —— 如「6.2」「5.30」
  b) 日记+日期 —— 如「日记6.2」「日记530」
  c) 自定义 —— 你自己输入格式
```

将用户选择记录为 `title_format`，存入配置。

如果用户选择自定义，让用户输入一个示例并解析出日期部分的位置。

### 4. 验证与保存

用获取的信息做一次测试读取：
- 读取 Base 最近 3 条记录以确认字段匹配
- 搜索最近 3 天妙记以测试标题格式搜索是否有效
- 设置 Base 视图按日期降序排列（最新在最前）：

```bash
lark-cli base +view-list --base-token <token> --table-id <table_id> --as user
# 找到 grid 类型视图的 view_id

lark-cli base +view-set-sort --base-token <token> --table-id <table_id> \
  --view-id <view_id> --as user \
  --json '{"sort_config":[{"field":"日期","desc":true}]}'
```

如果验证通过，用 Write 工具将以下内容写入当前项目的 Claude Code Memory 目录：

**写入路径**：`<当前项目 Memory 目录>/feishu-diary-config.md`

> 提示：先用 Glob 查找 `**/MEMORY.md` 定位 Memory 目录，然后在同目录下创建 `feishu-diary-config.md`。
> 如果找不到 Memory 目录，创建 `~/.claude/projects/<当前项目>/memory/feishu-diary-config.md`。

**写入内容**（将 `<...>` 替换为实际值）：

```markdown
---
name: feishu-diary-config
description: 飞书日记归档系统配置——Base/文档 token、标题格式、字段映射
metadata:
  type: project
---

## 飞书日记配置

### 存储位置
- **Base Token**: <token>
- **Table ID**: <table_id>
- **Done List 汇总文档**: <doc_token>
- **灵感与感悟汇总文档**: <doc_token>

### 标题格式
- **格式类型**: <纯日期/日记+日期/自定义>
- **匹配模式**: <用于搜索妙记的 title_format 参数>

### 字段映射
- 日期 → <字段名> (datetime)
- 标题 → <字段名> (text)
- 妙记链接 → <字段名> (text)
- Done List → <字段名> (text)
- 灵感与感悟 → <字段名> (text)
- 关键词 → <字段名> (text)
- 逐字稿摘要 → <字段名> (text)

### 偏好
- 妙记链接字段可点击查看逐字稿，无需额外写入逐字稿链接字段
```

### 5. 完成

告知用户配置完成，之后说"整理日记"即可触发增量归档。日常对话中提到日记相关内容时，AI 也会自动引用此配置来查询。
