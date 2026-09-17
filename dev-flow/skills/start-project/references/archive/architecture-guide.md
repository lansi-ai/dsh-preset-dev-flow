# 工程架构地图生成指南 (architecture-guide.md)

> **📦 已并入 `project-rules-guide.md`（v2.2）**——新项目按单入口生成，本文仅作历史参考。

## 01. 技能定位与生成目标
本指南用于指导 AI 在 `start-project` 阶段 2（或单独执行 `/init-l2-new`）时，读取 `docs/prd-and-design.md` 提取出的业务模块与架构形态，**正向生成一份确定性的工程目录映射与数据流向文件 `architecture.md`（保存于当前 IDE 的规则目录 `{{RULES_DIR}}` 中）**。

它的核心使命是：**消除架构腐化（Architecture Drift）**，明确每一个新创建的文件“必须放在哪里”，以及不同模块之间如何合规地进行数据交互。

生成的目标文件必须包含 Frontmatter 元数据，确保 AI 在新建文件或重构模块时**全局强制应用 (`alwaysApply: true`)** 目录约定与分层架构。

---

## 02. 编写与生成原则
1. **Frontmatter 必备**：文件顶部必须注入 YAML Frontmatter（`alwaysApply: true`, `globs: "*"`）。
2. **形态自适应（Architecture Adaptive）**：根据 PRD 确定的应用形态（BS Web / CS 桌面 / Mobile App / 后端 API），将通用的分层架构转译为该形态的标准目录规范：
   - *BS (Web)*：UI 组件 (Components) ➔ 页面 (Pages/Views) ➔ 状态/Hooks ➔ API 服务 (Services)。
   - *CS / App (桌面/移动端)*：视图 (Views/Screens) ➔ 视图模型 (ViewModels/Providers) ➔ 领域模型 (Domain Models) ➔ 存储/网络服务 (Services/Repositories)。
   - *Backend (微服务/API)*：控制器/路由 (Controllers/Routers) ➔ 业务逻辑 (Services/UseCases) ➔ 数据持久层 (Repositories/DAOs) ➔ 实体类型 (Entities/DTOs)。
3. **语言与路径保留**：规则描述使用 **中文**，所有具体的文件夹路径、文件名、架构名词保留 **英文原文**（如 `src/services/`, `ViewModels`, `DTO`, `Repository`）。
4. **篇幅精炼**：控制在 **80–100 行**以内，采用结构清晰的 Markdown 列表与判定表，禁止冗长的理论解释。

---

## 03. 必须包含的 5 个核心章节与生成模板

生成的 `architecture.md` **必须且只能** 包含以下 5 个章节（含 Frontmatter）：

```markdown
---
description: 工程项目目录结构地图、模块划分与数据分层流动规范
globs: "*"
alwaysApply: true
---

# 工程架构地图 (architecture.md)

## 01. 目录映射与文件放置规则
[AI 将根据 PRD 的技术选型与形态，自动填入如下标准目录映射]
- `[根源码路径]/views` 或 `components/` 或 `screens/`：仅放置纯 UI 展示与交互层文件，禁止在此直接书写数据库 SQL、复杂业务算法或裸网络请求。
- `[根源码路径]/services` 或 `repositories/`：放置对外 API 调用、SDK 封装、本地数据库/文件 I/O 读写逻辑。
- `[根源码路径]/models` 或 `types/` 或 `dtos/`：统一放置业务实体、数据传输对象（DTO）及接口类型定义。
- `[根源码路径]/store` 或 `viewmodels/` 或 `controllers/`：放置状态管理、UI 逻辑响应及中转控制逻辑。
- **放置铁律**：创建任何新文件前，必须核对上述目录定义；禁止在根目录下放置游离的业务逻辑代码。

## 02. 架构分层与数据流向
- **单向数据流与依赖倒置**：
  - *前端/UI 应用*：`UI / View` ➔ `ViewModel / Store / Hook` ➔ `Service API` ➔ `Backend`（依赖单向向下，底层禁止反向依赖 UI 层）。
  - *后端/服务应用*：`Controller / Handler` ➔ `Service / Business Logic` ➔ `Repository / DAO` ➔ `Database`。
- **解耦隔离**：UI 组件不得绕过 logic/service 层直接修改全局状态或发起原生的网络/数据请求；数据解析与 Model 转换必须在 Service/Repository 层完成。

## 03. 新建文件定位判定表 (Placement Decision Matrix)
| 当你需要创建以下类型的文件时... | 请强制放置到以下目录： |
| :--- | :--- |
| API 接口封装 / HttpClient / DB 操作 | `[根源码路径]/services/` 或 `[根源码路径]/repositories/` |
| 跨页面复用的通用 Modal / Button / Atom UI | `[根源码路径]/components/common/` |
| 仅属于特定业务领域的私有组件/视图 | `[根源码路径]/modules/[domain]/components/` |
| 全局 Data DTO / Entity / Interface 定义 | `[根源码路径]/types/` 或 `[根源码路径]/models/` |
| 通用无状态纯函数（如 Date 格式化、Math 计算）| `[根源码路径]/utils/` |

## 04. 核心设计模式与模块拆分
- **契约定义与数据转换**：网络传输必须使用明确的数据传输对象（DTO），禁止直接将数据库 ORM 实体透传给 UI 层。字段命名转换（如 `snake_case` ➔ `camelCase`）统一在网络拦截器/序列化层处理。
- **组件/类拆分粒度**：单一文件代码行数建议控制在 **200 行以内**（高复杂度文件不超过 300 行），超越阈值必须拆分为子组件或提取 Helper/Utility 逻辑。
- **状态管理收敛**：跨页面/全局共享的状态（如用户 Auth、主题配置、全局缓存）统一收敛至 Store/Provider 中，组件局部状态仅限 UI 控制（如 Modal 开关、Form 输入缓存）。

## 05. 规则自我演进维护
本文件为架构静态约束。若后续开发中新增了业务子模块目录、拆分了现有服务、调整了数据流向或重构了分层关系，AI 必须按照 `workflow.md` 的场景 B 规则更新协议，同步正向重构本文件，禁止“架构地图与工程实际脱节”。
```