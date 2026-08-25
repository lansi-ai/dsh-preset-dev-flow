# 静态代码规范生成指南 (core-standards-guide.md)

## 01. 技能定位与生成目标
本指南用于指导 AI 在 `start-project` 阶段 2（或单独执行 `/init-l1-new`）时，读取 `docs/prd-and-design.md` 提取出的技术选型与业务约束，**正向生成一份具备强约束力、无歧义的静态基座规则文件 `core-standards.md`（保存于当前 IDE 的规则目录 `{{RULES_DIR}}` 中）**。

无论目标项目是 Web (BS)、桌面端 (CS)、移动端 (App)、微服务后端，还是使用任何编程语言（TypeScript, C#, Go, Python, Java, Rust 等），本指南均能自动适配生成对应的红线规范。

生成的目标文件必须包含 Frontmatter 元数据，确保 AI 在生成或重构代码时**全局强制应用 (`alwaysApply: true`)** 代码风格、类型安全与安全红线。

---

## 02. 编写与生成原则
1. **Frontmatter 必备**：文件顶部必须注入 YAML Frontmatter（`alwaysApply: true`, `globs: "*"`）。
2. **技术栈自适应（Stack Adaptive）**：根据 PRD 中确定的具体语言与框架，将通用的安全与质量原则转译为该语言的具体语法约束（例如：TS 禁用 `any`；C# 开启 `Nullable` 并禁用 `async void`；Go 强制校验 `if err != nil`；Python 强制引入 Type Hints 与 Pydantic）。
3. **语言描述规范**：规则描述与逻辑约束统一使用 **中文**；所有的技术栈名称、配置项、代码方法、文件路径务必保留 **英文原文**（如 `TypeScript`, `async/await`, `.env`）。
4. **篇幅精炼**：全篇严格控制在 **80–100 行**以内，采用 Markdown Checklist / 列表形式，拒绝冗长的解释性文字，只保留可被 AI 严格执行的硬性约束。

---

## 03. 必须包含的 6 个核心章节与生成模板

生成的 `core-standards.md` **必须且只能** 包含以下 6 个章节（含 Frontmatter）：

```markdown
---
description: 核心代码规范、安全红线与语法准则
globs: "*"
alwaysApply: true
---

# 静态代码规范 (core-standards.md)

## 01. 核心技术栈与环境约束
- **基础 Runtime 与语言版本**：[根据 PRD 自动填写，如：Node.js >= 20.0.0 / .NET 8.0 / Go 1.22 / Python 3.11]。
- **核心框架与依赖**：[根据 PRD 自动填写，如：Next.js + Tailwind / WPF + CommunityToolkit.Mvvm / Flutter / FastAPI]。
- **配置与环境隔离**：所有敏感配置、API 密钥、数据库连接串统一通过环境变量或 `.env` / `appsettings.json` 加载，严格隔离开发、测试与生产环境；禁止在源码中放置私有变量。

## 02. 代码风格与语法规范
- **类型安全底线**：[自动适配语言类型约束]
  - *TypeScript/Dart*：严格开启 `strict` 模式，严禁使用 `any` 类型；不确定类型统一使用 `unknown` 并做类型收窄。
  - *C#/.NET*：开启 `#nullable enable`，禁止使用无类型定义的 `object` 传参，避免未处理的 NullReferenceException。
  - *Go*：禁止忽略错误，所有返回 `err` 的函数必须显式处理（禁止使用 `_` 忽略关键错误）。
  - *Python*：所有函数参数与返回值必须标注 Type Hints，禁止使用动态模糊类型。
- **并发与异步范式**：统一使用该语言推荐的现代异步范式（如 `async/await`、Goroutine + Channel、Task/ValueTask），禁止在异步方法中使用同步阻塞（如禁用 `.Result` / `.Wait()`，C# 中严禁使用 `async void`）。
- **命名范式**：类/接口/组件统一使用 `PascalCase`，变量/函数/方法统一使用 `camelCase`（Go/Python 遵照各自标准 `snake_case` 或导出大写规则），常量统一使用 `UPPER_SNAKE_CASE`。

## 03. 绝对禁区与安全红线 (Red Lines)
- 🚫 **严禁硬编码凭据**：API Keys、Tokens、数据库密码、JWT Secret 绝对禁止硬编码在任何源码文件中，必须通过环境变量或配置服务注入。
- 🚫 **严禁逃避类型与静态检查**：禁止使用 `@ts-ignore`、`#pragma warning disable` 或盲目 `@suppress` 规避编译器/Linter 报错，特殊情况必须附带充分的注释说明。
- 🚫 **严禁注入式安全漏洞**：
  - 前端/客户端：禁止使用未过滤的字符串动态构建 HTML/UI 节点（防范 XSS）。
  - 后端/数据层：禁止使用未经转义的动态字符串拼接 SQL / NoSQL 语句，所有查询必须使用参数化查询（ORM / Prepared Statements）。

## 04. 错误处理与日志规范
- **异常捕获范式**：所有网络 I/O、文件读写、数据库操作及外部服务调用，必须包裹在 `try-catch` / 显式 Error Check 块内。
- **结构化异常类型**：抛出异常必须使用结构化的自定义异常类（如 `AppError` / `DomainException`），包含统一的 `Code`、`Message` 与 `Details` 字段，禁止直接抛出裸字符串。
- **日志脱敏与控制**：生产环境严禁使用裸打印（如 `console.log` / `print()`），必须使用统一的 Logger 模块；日志记录中严禁包含用户密码、Token、支付信息等敏感数据。

## 05. 性能与安全底线
- **边界 Schema 强制校验**：所有外部传入的数据（网络 API 响应、HTTP 请求体、本地文件读取）在进入核心业务逻辑前，必须通过 Schema 工具（如 `Zod` / `Pydantic` / `FluentValidation`）进行强校验。
- **高频事件与渲染控制**：高频触发的操作（如 UI 搜索输入、窗口 Resize、滚动事件）必须施加防抖 (Debounce) 或节流 (Throttle) 处理。
- **资源释放保障**：所有占用系统资源的对象（如数据库连接、文件句柄、网络 Socket、Event 订阅）必须使用自动释放机制（如 `using` / `defer` / `try-finally`）保障内存与句柄及时回收。

## 06. 规则自我演进维护
本文件为静态硬约束。若后续开发中引入了新的核心库/SDK、更改了安全要求或调整了代码风格，AI 必须按照 `workflow.md` 的场景 A 规则更新协议，同步正向重构本文件，禁止“规范与代码脱节”。
```