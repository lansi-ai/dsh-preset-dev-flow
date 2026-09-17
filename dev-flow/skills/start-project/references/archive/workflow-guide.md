# 工作流与协作 SOP 指南 (workflow-guide.md)

> **📦 已并入 `project-rules-guide.md`（v2.2）**——新项目按单入口生成，本文仅作历史参考。

## 01. 技能定位与生成目标
本指南用于指导 AI 在项目生命周期中，**执行任何代码修改、功能迭代、架构重构或规则变更时，正向生成/维护 `workflow.md`（保存于当前 IDE 的规则目录 `{{RULES_DIR}}` 中）**。

它的核心使命是：**约束 AI 的编码行为流（Coding Agent Workflow）**，确保 AI 在动手前先澄清微观业务与 API 契约、编码时遵循规范、编码后严格自检，并具备**根据项目演进动态维护/更新分层规则体系的能力**。

生成的目标文件必须包含 Frontmatter 元数据，确保 AI 在执行任何开发任务时**全局强制应用 (`alwaysApply: true`)** SOP 流水线。

---

## 02. 编写与生成原则
1. **Frontmatter 必备**：文件顶部必须注入 YAML Frontmatter（`alwaysApply: true`, `globs: "*"`）。
2. **业务与工程双闭环（Domain & Engineering Closed-Loop）**：任务必须经过 `微观业务与契约对齐 ➔ 上下文预检 ➔ API/Schema 契约设计 ➔ 增量编码 ➔ 静态自检 ➔ 看板同步与 Git 提交` 6 个环节。
3. **规则演进机制（Rule Evolution Protocol）**：当遇到技术栈升级、架构重构或流程变更等特殊情况时，规范 AI 对各层规则文件进行正向更新，保持规则与代码 100% 同步。
4. **篇幅精炼**：结构清晰，指令明确。

---

## 03. 必须包含的核心章节与生成模板

生成的 `workflow.md` **必须且只能** 包含以下章节（含 Frontmatter）：

```markdown
---
description: AI 编码与工程协作 SOP、微观业务契约对齐、上下文预检流水线与规则自我演进协议
globs: "*"
alwaysApply: true
---

# 工作流与协作 SOP (workflow.md)

## 00. 编码前的业务与契约确认 (Context Alignment)
在开始编写任何 DTO / Schema 或业务逻辑前，AI **严禁直接盲目写代码**，必须先针对当前 Task 进行极简的微观业务与契约确认：
1. **主流程路径**：明确当前代码 Task 处理的具体动作与核心结果（Input/Output）。
2. **异常与逆向状态 (Unhappy Path)**：确认错误拦截（如：重复提交、权限不足、外部调用失败）及给前端/用户的返回状态。
3. **数据校验规则**：确认字段的硬性约束（如：长度、必填项、数值范围、唯一性校验）。
4. **MVP 交付边界**：确认当前 Task 涉及的代码范围，不在本次 Task 盲目扩张无关功能。

👉 **AI 执行指令**：在 Step 启动时，AI 抛出 `【编码前契约确认】` 并提出 2–3 个问题。
**在用户回答后，AI 需回复一句话总结契约，并询问：“确认没问题我就开始写 Schema 和代码了？”** 待用户给出肯定答复后方可动工。

## 01. 任务开始前的预检流水线 (Pre-execution Protocol)
契约确认完成后，AI 必须执行以下 3 步工程预检：
1. **读取 L3**：核对 `active-context.md`，确认任务符合当前 Sprint 目标与“下一步即时行动”。
2. **核对 L2**：确认即将创建/修改的文件路径是否符合 `architecture.md` 的目录映射约定与单向依赖规则。
3. **检查 L1**：确认技术栈版本与安全红线（如禁止硬编码凭据、防 SQL 注入）。

## 02. 编码与实现 SOP (Coding & Implementation Standard)
- **契约优先与代码即文档 (API-First / 方案 A)**：
  - 必须优先定义 DTO/Schema，确保 Swagger/OpenAPI 自动化生成。
  - 强制使用统一响应格式包装（如 `ApiResponse<T>`，包含 `code`, `data`, `message`）。
  - 后端/Schema 为**唯一类型源头**，前端类型直接自动导出或推导，禁止前端手动编写重复的平行接口定义。
- **开关式 Mock 规范**：前端网络请求层必须支持通过环境变量（如 `VITE_USE_MOCK`）或 MSW 无缝切换真实 API 与 Mock 数据。
- **增量实现与颗粒度控制**：
  - 按照 “定义 DTO/Schema ➔ 实现底层 Logic/Service ➔ 接入 UI/Controller ➔ 错误校验” 的顺序单向推进。
  - 单个 Task 处理文件严格控制在 **1–3 个** 内部相关文件，严禁一次性生成带 `TODO` 占位符的大量伪代码。
- **注释与自解释**：新增函数必须附带极简的功能注释，复杂逻辑必须说明设计意图；严禁留下 `TODO` 或未完成的临时伪代码。

## 03. 交付前的质量自检链 (Quality Check Gate)
代码编写完成后，在向用户汇报或标记完成前，必须通过以下 Check:
- [ ] **业务逻辑自检**：是否满足 00 节对齐的正常路径与异常边界流转。
- [ ] **语法与类型自检**：无未处理的 `any`/裸类型，无缺失的错误捕获 (`try-catch` / `if err != nil`)。
- [ ] **契约与响应自检**：所有 API 均经过统一 `ApiResponse<T>` 范型包装，TypeScript 类型由单一源头生成。
- [ ] **路径与放置自检**：新文件路径与命名完全匹配 `architecture.md`。
- [ ] **安全自检**：未引入任何凭据泄漏、动态 SQL 拼接或未校验的外部输入。
- [ ] **Git 提交准备**：拟定的 Commit Message 完全符合 `git-commit-guide.md` 的 Conventional Commits 格式。

## 04. 特殊场景：规则自我演进与重构协议 (Rule Maintenance Protocol)
当发生以下特殊场景时，AI **必须主动提醒用户并同步更新对应的规则文件**，禁止“规则与代码脱节”：
- 🔄 **场景 A：技术栈/规范变更（更新 L1）**
  - *触发条件*：引入新的核心库/SDK、更改安全要求、调整代码风格。
  - *动作*：更新 `core-standards.md` 中的技术栈与语法规范章节。
- 🏗️ **场景 B：目录/架构重构（更新 L2）**
  - *触发条件*：新增业务模块目录、拆分现有服务、调整数据流向或分层关系。
  - *动作*：更新 `architecture.md` 中的目录映射与分层规则。
- ⚙️ **场景 C：协作流程调整（更新 workflow.md）**
  - *触发条件*：新增自动化测试要求、改变 Commit/Review 规范或修改 SOP 节点。
  - *动作*：更新 `workflow.md` 本身的 SOP 流程。

## 05. 任务完成后的状态闭环 (Post-execution Synchronization)
任务完成并通过质量自检后，AI 必须**自动执行状态同步**：
1. **更新 L3 文本看板**：在 `active-context.md` 中将完成项标记为 `[x]`，并更新“下一步即时行动”。
2. **同步 L3 美化看板**：重新渲染并覆盖更新 `docs/active-context.html`，保持 HTML 与 MD 看板 100% 一致。
3. **输出 Git Commit 指令**：严格按照 `git-commit-guide.md` 规范，为用户直接生成可复制执行的 `git add . && git commit -m "..."` 终端命令。
4. **输出变更汇报**：向用户汇报改动的代码文件列表，若触发了规则变更，需明确告知规则文件的修改内容。
```