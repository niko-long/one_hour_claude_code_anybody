# Claude Code Workshop

A single-page, static HTML tutorial for learning Claude Code on macOS. Covers shell basics, Git version control, Claude Code installation and usage, project structure concepts, prompt engineering tips, and the Claude agent/context/MCP ecosystem.

Built as a reference guide for a casual ~60-minute hands-on session among friends and peers.

## Live Page

`https://<your-username>.github.io/claude-code-workshop/`

## What's Inside

- **Shell basics** — opening Terminal on macOS, common commands (`cd`, `ls`, `mkdir`, etc.)
- **Git fundamentals** — version control workflow, branching, using Git as a safety net for AI-assisted coding
- **Claude product overview** — model tiers (Opus / Sonnet / Haiku), claude.ai vs Claude Code vs API
- **Claude Code setup** — installation (native / Homebrew), authentication, troubleshooting
- **Core usage** — slash commands, interaction patterns, permission model
- **CLAUDE.md** — project-level context configuration, layered configs, `.claudeignore`
- **Frontend/backend concepts** — separation of concerns, typical project structure
- **Prompt techniques** — specifying tech stacks, letting Claude ask clarifying questions, step-by-step execution
- **Hands-on project** — building a small tool from scratch using Claude Code (CLI tool / REST API / data script)
- **Advanced topics** — agent loop, context window management, Skills, Subagents, MCP, Hooks

## Tech Stack

Just a single `index.html` file. No build step, no dependencies.

- HTML + CSS + vanilla JS
- Google Fonts (Noto Sans SC, JetBrains Mono, Source Serif 4)
- Scroll-driven navigation highlighting and progress bar

## Deploy

Rename the file to `index.html`, push to a GitHub repo, and enable GitHub Pages:

```bash
git init
git add index.html
git commit -m "add workshop page"
git branch -M main
git remote add origin https://github.com/<username>/claude-code-workshop.git
git push -u origin main
```

Then go to **Settings → Pages → Branch: main → Save**. The site will be live at the URL above within a couple of minutes.

## License

Free to use and modify.

---

# Claude Code 工作坊

一个单页静态 HTML 教程，用于学习 macOS 上的 Claude Code。内容涵盖 Shell 基础、Git 版本控制、Claude Code 安装与使用、项目结构概念、Prompt 技巧，以及 Claude 的 Agent / Context / MCP 生态。

为朋友之间约 60 分钟的非正式动手分享而制作。

## 在线地址

`https://<你的用户名>.github.io/claude-code-workshop/`

## 内容概要

- **Shell 基础** — macOS 打开终端、常用命令（`cd`、`ls`、`mkdir` 等）
- **Git 基础** — 版本控制工作流、分支操作、在 AI 辅助开发中用 Git 做安全网
- **Claude 产品概览** — 模型层级（Opus / Sonnet / Haiku）、claude.ai vs Claude Code vs API
- **Claude Code 安装** — 原生安装器 / Homebrew、认证流程、故障排查
- **基础操作** — 斜杠命令、交互模式、权限确认
- **CLAUDE.md** — 项目级上下文配置、分层配置、`.claudeignore`
- **前后端分离** — 概念说明、典型项目结构示例
- **Prompt 技巧** — 明确技术栈、让 Claude 反问确定方案、分步执行
- **实战项目** — 用 Claude Code 从零构建小工具（CLI / REST API / 数据脚本）
- **进阶知识** — Agent 循环、上下文窗口管理、Skills、Subagents、MCP、Hooks

## 技术栈

只有一个 `index.html` 文件，无需构建，无依赖。

- HTML + CSS + 原生 JS
- Google Fonts（Noto Sans SC、JetBrains Mono、Source Serif 4）
- 滚动驱动的导航高亮和阅读进度条

## 部署

将文件重命名为 `index.html`，推送到 GitHub 仓库，开启 GitHub Pages：

```bash
git init
git add index.html
git commit -m "add workshop page"
git branch -M main
git remote add origin https://github.com/<用户名>/claude-code-workshop.git
git push -u origin main
```

进入仓库 **Settings → Pages → Branch: main → Save**，几分钟后即可通过上面的地址访问。

## 许可

自由使用和修改。
