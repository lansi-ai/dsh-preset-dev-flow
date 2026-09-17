# 激活上下文规则生成指南 (active-context-guide.md)

## 01. 技能定位与生成目标
本指南用于指导 AI 在 `start-project` 阶段 2（或后续任务更新）时，读取 `docs/prd-and-design.md` 提取出的第一阶段 MVP 目标，**生成/更新单一产物 `docs/active-context.md`**：
- **`docs/active-context.md`**：存放在 `docs/` 目录中的纯文本动态看板（滚动窗口），供 AI 快速读取与维护，人类也可直接阅读。
- 看板形态：仅 MD（`docs/active-context.md`）；不生成/不维护 HTML 副本。
- C 类小任务不强制更新看板（除非碰里程碑），日末/发版统一收口。

---

## 02. 编写与生成原则
1. **无 Frontmatter**：本文件是项目文档（看板），不是 IDE 注入配置——顶部不写任何 YAML Frontmatter 元数据。
2. **目标聚焦（Sprint Focused）**：仅聚焦于当前阶段（通常是 MVP 1.0 或第 1 个迭代周期）的核心功能，控制在 ≤100 行以内，避免将远期规划混入当前上下文。
3. **状态可追踪（State Trackable）**：采用清晰的 Task Checklist 表达（`[ ]` 未开始, `[/]` 进行中, `[x]` 已完成）。
4. **单一产物（MD Only）**：只落盘 `docs/active-context.md`，不生成 HTML 或其它格式的副本。
5. **看板更新协议**：任务节点的收口更新按 `docs/PROJECT-RULES.md` §3 工作流的完成门禁执行；**C 类小任务不强制更新看板**（除非碰里程碑），日末/发版统一收口。

---

## 03. 必须包含的核心章节与生成模板

### 模板：`docs/active-context.md`

```markdown
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
本文件为动态看板。每次完成任务节点或发生业务需求变更时，AI 必须按照 `docs/PROJECT-RULES.md` §3 工作流的更新协议，同步更新本文件（**仅 MD，不再生成/渲染 HTML 副本**）；C 类小任务不强制更新，日末/发版统一收口，确保任务状态与实际工程代码完美一致。
```
