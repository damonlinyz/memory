---
name: design-workflow
description: 设计工作流 - Stitch MCP + Claude CLI 生成 HTML，NIO AI 配色系统
metadata: 
  type: project
  originSessionId: 8f084d91-d3fd-482a-84b7-63f3e3e5b038
---

# 设计工作流（废弃 Ardot MCP）

## 背景

用户决定废弃 Ardot MCP 方案，原因：
- Shorthand 语法限制严（不支持 JSON 数组、链式引用）
- Auto Layout 行为不稳定
- 调试成本高，多次手动调整仍错乱

## 新方案：Stitch MCP + Claude CLI

**流程：**
```
设计规范（Markdown） → Claude Code 生成 HTML → 本地直接查看
```

**工具链：**
- **Stitch MCP** — 输出设计规范、配色方案
- **Claude CLI** — 直接生成 HTML 代码
- **本地浏览器** — 打开生成的 HTML 查看

**配色系统：** NIO AI Brand 2.0（`NIO_AI_ColorSystem.html`）

## NIO AI 配色系统

### 核心色板

| 名称 | Hex | 用途 |
|------|-----|------|
| Deep Night | #0F2841 | 背景、封面、深色卡片 |
| Blue Hour | #335176 | 表头、Badge、图标 |
| First Blue | #DCEAFA | 浅色卡片、交替行 |
| Teal | #00BEBE | 全局强调色、数字、Agent Badge |
| Golden Hour | #FAEBDC | Callout 提示、暖色区 |
| Mid Noon | #7D6E62 | 暖色区文字 |
| White | #FFFFFF | 内容页底色 |
| Cloud Gray | #F0F0F2 | 奇数行、图例栏 |
| Cool Gray | #DCDCDE | 分隔线、描边 |
| Silver Gray | #8D8D8F | 脚注、辅助文字 |
| Dark Gray | #54585A | 次级文字 |
| Off Black | #1A1A1A | 正文主色 |

### 语义角色

- **背景**：Deep Night（深色区）、White（内容区）、Cloud Gray（页面背景）
- **强调**：Teal（唯一强调色）
- **表头**：Blue Hour
- **正文**：Off Black
- **辅助**：Silver Gray

### 使用规范

**推荐：**
- Teal 只作强调色，不作大面积背景
- 深色背景用 White 或 Teal 文字
- 表格交替 Cloud Gray / White
- Badge 语义：Agent=Teal、Copilot=Blue Hour、暂不适合=Silver Gray

**禁止：**
- 不在白色背景上使用 First Blue 文字（对比度不足）
- 不混用多个亮色强调色
- 不使用渐变色（系统纯色为主）
- Golden Hour 仅限 Callout

## 设计文件路径

**配色系统：**
`/Users/damon.lin/Downloads/NIO_AI_ColorSystem(2).html`

**Demo 设计稿：**
- 设计规范：`/Users/damon.lin/docs/superpowers/specs/2026-05-22-travel-share-homepage-design.md`
- 生成的 HTML：`/Users/damon.lin/docs/superpowers/specs/2026-05-22-travel-share-homepage.html`

## 后续计划

- [x] 废弃 Ardot MCP 方案
- [x] 建立基于 NIO 配色系统的 DESIGN.md 模板
- [x] 用 Claude CLI 生成第一个 HTML 原型
- [x] 测试 Stitch MCP 与配色系统的衔接（Demo 完成）

---

## 旧方案备份：Ardot MCP

~~Ardot MCP 存在严重问题，已废弃~~

**原 MCP Server：**
```
/Applications/Ardot.app/Contents/Library/ArdotMCP.app/Contents/MacOS/mcp-server
```

**原配置文件：** `~/.mcp.json`

~~设计规范文档：~~ `~~/Users/damon.lin/Documents/governor/governor/03-Skill库/superpowers/skills/brainstorming/docs/superpowers/specs/2026-05-22-obsidian-ardot-mcp-design.md~~`