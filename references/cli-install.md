# 飞书 CLI 安装指南

> 来源: https://open.feishu.cn/document/no_class/mcp-archive/feishu-cli-installation-guide.md

## 前置依赖

- **Node.js**（附带 npm/npx）—— 必需

## 安装步骤

### 第 1 步：安装 CLI 及技能包

```bash
npm install -g @larksuite/cli
```

安装必需的技能包：

```bash
npx -y skills add https://open.feishu.cn --skill -y
```

### 第 2 步：配置应用凭证

```bash
lark-cli config init --new
```

此命令会引导创建飞书应用，部分步骤需要在浏览器中完成授权。

### 第 3 步：登录

```bash
lark-cli auth login --recommend
```

将生成的授权链接发给用户，用户在浏览器中点击确认完成登录。

### 第 4 步：验证安装

```bash
lark-cli auth status
```

如果返回认证状态正常且显示用户信息，说明安装和登录均已完成。

## 常见问题

- 如果 `lark-cli` 命令未识别，关闭终端重新打开，或检查 npm 全局安装路径是否在 PATH 中。
- 授权链接需在有效期内完成（通常 5 分钟），过期后重新运行 `lark-cli auth login --recommend` 即可。
- 安装完成后，还需要安装 `lark-base`、`lark-minutes`、`lark-doc` 三个技能包才能使用本日记归档 skill。本 skill 会在首次配置时自动检查。
