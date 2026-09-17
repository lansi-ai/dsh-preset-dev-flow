# Git 提交规范与协作指南 (git-commit-guide.md)

> **📦 已并入 `project-rules-guide.md`（v2.2）**——新项目按单入口生成，本文仅作历史参考。

## 01. 技能定位与生成目标
本指南用于约束 AI 及团队开发者在项目开发过程中，每次代码变动或任务交付时的 **Git Commit Message 格式、提交颗粒度与流转 SOP**。

它的核心目标是：
1. **历史可追溯**：让 Git 日志成为最直观、无需额外维护的工程过程留痕。
2. **看板自然绑定**：提交信息直接与 `active-context.md` 中的任务步骤（Step）一一对应。
3. **变更清晰**：规范提交类型，便于后续自动生成 Changelog、自动化 CI/CD 以及 Code Review。

---

## 02. Commit Message 核心格式

所有的 Commit 提交信息必须严格遵循以下 Conventional Commits（约定式提交）结构：

```text
<type>(<scope>): <short summary>

[optional body]
```

### 1. 提交类型 (`<type>`) 规范

| 类型 (Type) | 适用场景 | 示例 |
| :--- | :--- | :--- |
| **feat** | 新增业务功能 / 接口 Schema / 页面组件 | `feat(api): 定义 User DTO 与 Swagger Schema` |
| **fix** | 修复 Bug / 纠正类型定义 / 处理边界异常 | `fix(auth): 修复 JWT 拦截器未捕获 Token 问题` |
| **docs** | 仅修改文档 / 规则文件 / 看板落盘更新 | `docs(board): 更新 active-context 看板为步骤2已完成` |
| **refactor**| 重构代码（不改变现有功能与接口逻辑） | `refactor(user): 提取用户校验逻辑至 Service 层` |
| **style** | 格式化代码、补充注释、修正错别字（不影响逻辑） | `style(dto): 为 ApiResponse 添加字段注释` |
| **chore** | 构建配置修改、依赖包安装或环境搭建 | `chore(deps): 安装 Zod 与 MSW 依赖包` |
| **test** | 增加、修改单元测试或集成测试 | `test(user): 补全用户登录接口冒烟测试` |

### 2. 作用域 (`<scope>`) 规范
用简短的模块或功能领域名注明改动的影响范围（如 `api`, `auth`, `user-ui`, `db`, `config`, `board`）。

### 3. 主题 (`<short summary>`) 规范
* 使用清晰、准确的动名词短语，**控制在 50 个字符以内**。
* 明确写清具体交付了什么（如 `定义用户登录 DTO`，严禁使用 `修改代码`、`bugfix` 等模糊词汇）。

---

## 03. 编码与提交 SOP (Git Commitment SOP)

### 1. 提交颗粒度控制 (Commit Scope Gate)
- **小步快跑与原子化**：严禁把整个 Sprint 或多个无关步骤合并为一次巨型提交。**每完成 `active-context.md` 中的一个子任务节点（处理 1–3 个关联文件），即触发一次 Commit**。
- **职责单一**：一次 Commit 只做一件事（例如：`feat(api)` 接口定义与 `docs(board)` 看板更新可以伴随提交，但严禁将 `feat(user)` 与 `fix(order)` 混在一个 Commit 中）。

### 2. 契约变更提交强提示
- 当提交涉及 **DTO / Schema 破坏性接口变更** 时，Commit Message 必须在 type/scope 后添加 `!` 并显式注明，提醒前端同步刷类型：
  `feat(api)!: 修改 User DTO 字段结构 (破坏性变更/需重新导出类型)`

### 3. 看板状态更新同步提交
- 当完成一个阶段性步骤（Step）并更新了看板后，提交信息需显式绑定看板节点：
  `docs(board): 完成步骤 2 (Schema定义)，同步更新 active-context (MD+HTML)`

---

## 04. AI 提交触发命令与工作流模板

当用户在对话中提示 **“提交代码”**、**“Git Commit”** 或 **“完成当前 Task”** 时，AI 必须检查 `git status` 并生成符合标准的终端脚本：

### 场景 A：日常增量 Task 提交
```bash
git add src/types/user.ts src/controllers/userController.ts
git commit -m "feat(user): 实现用户登录 Controller 与 Schema 校验"
```

### 场景 B：步骤节点完成 + 看板双落盘同步提交
```bash
git add src/ {{RULES_DIR}}/active-context.md docs/active-context.html
git commit -m "docs(board): 完成步骤 2 DTO 契约定义与 Swagger 导出，更新任务看板"
```