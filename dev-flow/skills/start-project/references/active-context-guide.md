# 激活上下文规则生成指南 (active-context-guide.md)

## 01. 技能定位与生成目标
本指南用于指导 AI 在 `start-project` 阶段 2（或单独执行 `/init-l3-new` 及后续任务更新）时，读取 `docs/prd-and-design.md` 提取出的第一阶段 MVP 目标，**同步正向生成/更新两个格式的文件**：
1. **`active-context.md`**：存放在当前 IDE 的规则目录 `{{RULES_DIR}}` 中，带 Frontmatter Header，供 AI 快速读取与维护的纯文本动态看板。
2. **`docs/active-context.html`**：存放在 `docs/` 目录中，供人类开发者在浏览器中直接打开、高颜值、易于直观查看的**交互美化版看板**。

---

## 02. 编写与生成原则
1. **Frontmatter 必备（仅限 MD 版本）**：`active-context.md` 顶部必须注入 YAML Frontmatter（`alwaysApply: true`, `globs: "*"`）。
2. **目标聚焦（Sprint Focused）**：仅聚焦于当前阶段（通常是 MVP 1.0 或第 1 个迭代周期）的核心功能，控制在 60–80 行以内，避免将远期规划混入当前上下文。
3. **状态可追踪（State Trackable）**：采用清晰的 Task Checklist 表达（`[ ]` 未开始, `[/]` 进行中, `[x]` 已完成）。
4. **HTML 零依赖自包含（Zero Dependency）**：生成的 HTML 文件必须将样式（CSS）内置，无需任何外部 CDN 或 JS 库，确保离线秒开、极其轻量。
5. **双向同步落盘**：每次生成或更新 Markdown 版看板时，必须同步重新渲染并覆盖更新 `docs/active-context.html`。

---

## 03. 必须包含的核心章节与生成模板

### 模板 1：Markdown 版本 (`active-context.md`)

```markdown
---
description: 项目当前 Sprint 激活上下文与动态任务看板
globs: "*"
alwaysApply: true
---

# 激活上下文与任务看板 (active-context.md)

## 01. 当前迭代目标 (Current Sprint Goal)
- **版本阶段**：[如：MVP 1.0 基础原型 / Phase 1 核心功能开发]
- **核心业务价值**：[用一句话概括本阶段要交付的核心价值]
- **关键交付物**：[如：可运行的后端 API + 前端登录/看板页面]

## 02. 任务清单与状态 (Task Kanban)
- [ ] **步骤 1: 基础设施与脚手架搭设**
  - [ ] 初始化项目基础结构与配置文件
  - [ ] 配置环境变量与本地依赖环境
- [ ] **步骤 2: 接口 Schema/DTO 定义与 Swagger 导出 (API-First 方案 A)**
  - [ ] 编写全局统一 `ApiResponse<T>` 范型响应结构与错误码
  - [ ] 编写核心 DTO / Schema 校验逻辑并导出 Swagger/OpenAPI
  - [ ] 配置前端基于 Swagger 的类型自动导出与开关式 Mock 环境
- [ ] **步骤 3: 核心逻辑与 UI/UX 界面实现**
  - [ ] 后端实现 Controller / Business Logic / Repository
  - [ ] 前端搭建页面组件并接入 Mock/真实数据流
- [ ] **步骤 4: 前后端联调测试与收尾**
  - [ ] 切换真实 API 联调与边界条件/异常处理校验
  - [ ] 核心流程冒烟测试通过

## 03. 关键决策与架构遗留 (Key Decisions & Context)
- **已做出的关键技术决策**：
  - [决策 1]：[如：采用 API-First 方案 A，后端 Schema 为类型唯一源头]
  - [决策 2]：[如：统一使用 ApiResponse<T> 响应包装与 MSW 开关式 Mock]
- **风险与技术债记录**：
  - [暂存项 1]：[如：MVP 阶段暂不引入 Redis 缓存]

## 04. 下一步即时行动 (Next Immediate Actions)
- **当前正在处理**：步骤 1 基础设施与脚手架搭设。
- **即将创建的文件**：[列出即将创建的前 1-2 个文件路径]
- **AI 交互指令提示**：后续会话可直接提示 “按照 active-context.md 的下一步继续执行”。

## 05. 规则自我演进维护
本文件为动态看板。每次完成任务节点或发生业务需求变更时，AI 必须按照 `workflow.md` 的场景 C 规则更新协议，同步正向重构 Markdown 版与渲染 `docs/active-context.html`，确保任务状态与实际工程代码完美一致。
```

### 模板 2：HTML 美化版本 (docs/active-context.html)
AI 在生成或更新时，需将 Markdown 中的真实数据填入以下内置暗黑模式 CSS 的现代卡片风格 HTML 模板中（保存至 docs/active-context.html，无需 Frontmatter）：
``` html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>激活上下文与任务看板</title>
  <style>
    :root {
      --bg: #0f172a;
      --card-bg: #1e293b;
      --text-main: #f8fafc;
      --text-muted: #94a3b8;
      --accent: #38bdf8;
      --success: #34d399;
      --warning: #fbbf24;
      --border: #334155;
    }
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
      background-color: var(--bg);
      color: var(--text-main);
      margin: 0;
      padding: 24px;
      line-height: 1.6;
    }
    .container { max-width: 880px; margin: 0 auto; }
    .header {
      border-bottom: 2px solid var(--border);
      padding-bottom: 16px;
      margin-bottom: 24px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .header h1 { margin: 0; font-size: 1.5rem; color: var(--accent); }
    .badge {
      background: rgba(56, 189, 248, 0.1);
      color: var(--accent);
      padding: 4px 12px;
      border-radius: 20px;
      font-size: 0.85rem;
      border: 1px solid rgba(56, 189, 248, 0.3);
    }
    .card {
      background: var(--card-bg);
      border: 1px solid var(--border);
      border-radius: 12px;
      padding: 20px;
      margin-bottom: 20px;
      box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.2);
    }
    .card h2 { margin-top: 0; font-size: 1.1rem; color: var(--text-main); border-bottom: 1px solid var(--border); padding-bottom: 8px; }
    .task-list { list-style: none; padding: 0; margin: 0; }
    .task-item {
      padding: 8px 12px;
      margin: 6px 0;
      border-radius: 6px;
      background: rgba(255, 255, 255, 0.02);
      display: flex;
      align-items: center;
      gap: 10px;
    }
    .task-item.sub { margin-left: 24px; font-size: 0.95rem; color: var(--text-muted); }
    .status-icon { font-weight: bold; font-family: monospace; }
    .done { color: var(--success); text-decoration: line-through; opacity: 0.7; }
    .in-progress { color: var(--warning); font-weight: 600; }
    .todo { color: var(--text-muted); }
    .highlight-box {
      background: rgba(56, 189, 248, 0.05);
      border-left: 4px solid var(--accent);
      padding: 12px 16px;
      border-radius: 0 8px 8px 0;
    }
    ul { padding-left: 20px; color: var(--text-muted); }
    li { margin-bottom: 6px; }
    strong { color: var(--text-main); }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>🚀 激活上下文看板</h1>
      <span class="badge">MVP 1.0 阶段</span>
    </div>

    <!-- 01. 目标 -->
    <div class="card">
      <h2>🎯 当前迭代目标</h2>
      <p><strong>核心价值：</strong>[根据实际内容自动填入]</p>
      <p><strong>关键交付物：</strong>[根据实际内容自动填入]</p>
    </div>

    <!-- 02. 看板 -->
    <div class="card">
      <h2>📋 任务看板 (Task Kanban)</h2>
      <ul class="task-list">
        <!-- AI 将根据 task 状态（[x], [/], [ ]）自动映射对应的 CSS 类名（done / in-progress / todo） -->
        <li class="task-item in-progress">
          <span class="status-icon">[/]</span>
          <span><strong>步骤 1: 基础设施与脚手架搭设</strong></span>
        </li>
        <li class="task-item sub todo">
          <span class="status-icon">[ ]</span>
          <span>初始化项目基础结构与配置文件</span>
        </li>
      </ul>
    </div>

    <!-- 03. 决策 -->
    <div class="card">
      <h2>💡 关键决策与遗留记录</h2>
      <ul>
        <li><strong>已选决策：</strong> [根据实际内容自动填入]</li>
        <li><strong>技术债暂存：</strong> [根据实际内容自动填入]</li>
      </ul>
    </div>

    <!-- 04. 即时行动 -->
    <div class="card highlight-box">
      <h2 style="border:none; padding:0; margin-bottom:8px; color: var(--accent);">⚡ 下一步即时行动</h2>
      <p style="margin:0;"><strong>当前焦点：</strong> [根据实际内容自动填入]</p>
      <p style="margin:4px 0 0 0; font-size:0.9rem; color:var(--text-muted);">提示：直接在浏览器中打开本 HTML 即可随时查看最新进度。</p>
    </div>
  </div>
</body>
</html>
```