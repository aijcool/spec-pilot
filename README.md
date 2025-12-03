# Spec-Driven Development Template

基于用户体验要素的规范驱动开发模板。

---

## 环境准备

### 1. 安装 Claude Code

```bash
npm install -g @anthropic-ai/claude-code
claude login
```

### 2. 添加 MCP 工具

```bash
# Puppeteer（自主开发模式需要）
claude mcp add puppeteer -s user -- npx puppeteer-mcp-server

# 可选：其他常用 MCP
claude mcp add supabase -s user -- npx supabase-mcp-server
```

### 3. 验证

```bash
claude mcp list
```

---

## 使用方式

### 1. 复制模板到新项目

```bash
cp -r deliverable/* /path/to/your/project/
cd /path/to/your/project
```

### 2. 启动 Claude Code

```bash
claude
```

### 3. 开始设计

对 Claude 说：
```
设计方案
```

### 4. 确认后开发

方案确认后，对 Claude 说：
```
开始开发
```

---

## 项目结构

```text
project/
├── README.md                # 本文件 - 使用说明
├── CLAUDE.md                # 总纲（Claude 读取）
├── .claude/commands/        # 自定义命令
│   ├── design.md            # /project:design
│   ├── develop.md           # /project:develop
│   └── status.md            # /project:status
├── solution/                # Phase 1: 方案设计
│   ├── CLAUDE.md            # 方案设计指南
│   ├── 1_strategy.md        # 战略层
│   ├── 2_scope.md           # 范围层
│   ├── 3_structure.md       # 结构层
│   ├── prototype/           # 原型层
│   └── app_spec.txt         # 合并产出
└── src/                     # Phase 2: 代码开发
    ├── CLAUDE.md            # 开发指南
    ├── frontend/            # Next.js
    └── backend/             # FastAPI
```

---

## 工作流程

```
Phase 1: 方案设计（人机协作）
  你描述需求 → Claude 生成文档 → 你 Review → 确认

Phase 2: 代码开发（自主模式）
  Claude 自动开发 → Puppeteer 验证 → 循环直到完成
```

---

## 技术栈

| 服务 | 平台 |
|-----|------|
| 代码托管 | GitHub |
| 鉴权 | Clerk |
| 支付 | Stripe |
| 数据库 | Supabase |
| 后端 | Railway (Python + FastAPI) |
| 前端 | Vercel (Next.js) |

---

## 快捷命令

使用 `/project:命令名` 触发自定义命令：

| 命令 | 说明 |
|-----|------|
| `/project:design` | 开始方案设计（Phase 1）|
| `/project:develop` | 进入自主开发模式（Phase 2）|
| `/project:status` | 查看当前进度 |

或者直接用自然语言：

| 说法 | 效果 |
|-----|------|
| `设计方案` | 开始方案设计 |
| `生成战略层` | 生成 1_strategy.md |
| `生成范围层` | 生成 2_scope.md |
| `生成结构层` | 生成 3_structure.md |
| `生成原型` | 生成 prototype/ |
| `生成规格` | 合并生成 app_spec.txt |
| `开始开发` | 进入自主开发模式 |
| `当前状态` | 查看进度 |
