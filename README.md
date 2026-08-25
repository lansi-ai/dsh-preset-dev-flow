# dsh-preset-dev-flow

一个面向代码开发的 **DSH Agent 预设**：让 Agent 按一套"先澄清、再动手、交付出要自检、规则与代码同步"的开发流程来工作。

An **agent preset** for the DeepSeek Harness: a disciplined, sign-off-gated *development-process* agent built on the `standard` coding agent.

---

## 这是什么 / What it is

`dev-flow` 是基于 `standard`（功能完整的编码 Agent）的一个预设。它给 Agent 注入了你的开发流程纪律，并随身携带你的 `start-project` 技能：

A preset built on top of the `standard` full coding agent. It injects a development-process discipline into the agent and ships your `start-project` skill alongside it.

核心流程（写入 persona，常驻生效）：

- **头脑风暴先行**：接需求先按 5 维框架追问，收敛共识，拿到用户 **Sign-off** 才动手。
- **自动工程交付**（Sign-off 后）：自动检测 `{{RULES_DIR}}`，生成 `docs/prd-and-design.md` + 6 份规则文件（`core-standards`/`architecture`/`active-context`+HTML 看板/`git-commit-guide`/`workflow`）。
- **编码 SOP**：每任务先做"编码前契约确认"→ 预检 L3/L2/L1 → API-First（单一类型源头 + `ApiResponse<T>` + 开关式 Mock）→ 增量实现（1–3 文件/任务，禁 TODO 堆砌）→ 交付前质量自检链。
- **后置同步**：更新看板 MD+HTML、生成 Conventional Commit 命令、汇报变更。
- **规则自我演进**：技术栈/架构/流程变化时同步更新对应规则文件。
- **红线**：不硬编码凭据、不逃静态检查、防注入、结构化异常、Schema 强校验、资源自动释放。

Core behavior (injected into the persona, always active):

- **Brainstorm first**: on any request, ask structured questions, converge to a consensus, and require an explicit **Sign-off** before coding.
- **Auto engineering delivery** (after sign-off): detect `{{RULES_DIR}}` and generate `docs/prd-and-design.md` plus 6 rule files (`core-standards`, `architecture`, `active-context` + HTML board, `git-commit-guide`, `workflow`).
- **Coding SOP**: micro-contract confirmation → pre-flight L3/L2/L1 → API-First (single type source + `ApiResponse<T>` + switchable Mock) → incremental implementation (1–3 files/task, no TODO stubs) → quality gate.
- **Post-execution sync**: update the MD+HTML board, emit a Conventional Commit command, report changes.
- **Rule self-evolution**: update the matching rule file when the stack/architecture/process changes.
- **Red lines**: no hardcoded credentials, no type-check evasion, no injection, structured errors, schema validation, resource auto-release.

---

## 目录结构 / Layout

```
dsh-preset-dev-flow/
├── README.md
└── dev-flow/                       # 预设文件夹（复制进 $DSH_HOME/.agent-presets/ 即可）
    ├── agent.cordis.yml            # 预设组成：persona + 工具 + 计划模式 + 压缩 + 委托 + skill 发现根 + fetch_url
    ├── preset.yml                  # 显示名「开发流程」+ 描述
    └── skills/
        └── start-project/          # 随预设携带的技能
            ├── SKILL.md
            └── references/         # brainstorming / core-standards / architecture /
                                    # active-context / git-commit / workflow 参考指南
```

---

## 先决条件 / Prerequisites

- 一个**标准 DSH 部署**：本预设引用的工具（`dsh-tool-pwsh`、`dsh-tool-fs`、`dsh-skill-filesystem`、计划模式、压缩、委托等）都是 `standard` 自带的包；若你的部署没有它们，预设将无法挂载。
  A **standard DSH deployment**. This preset references the standard tool packages; if your deployment lacks them the preset will not mount.
- **`@deepseek-ai/dsh-fetch-url`**：本预设声明了 `fetch_url` 工具，需先安装（见下）。
  **`@deepseek-ai/dsh-fetch-url`**: this preset declares the `fetch_url` tool, so it must be installed first (see below).

---

## 安装 / Install

### 1. 安装工具插件（预设 `fetch-url` 行的前置条件） / Install the tool plugin

在本预设使用之前，把插件安装进你的 DSH profile（让它可被解析，但**不会**变成宿主全局）：

```bash
# 在你的 profile 目录里（例如 profiles/web）
# In your DSH profile directory (e.g. profiles/web)
cd <your dsh profile>
pnpm add https://github.com/lansi-ai/dsh-fetch-url
# 或者等价地： pnpm add github:lansi-ai/dsh-fetch-url
```

> 说明：预设 `agent.cordis.yml` 里的 `fetch-url` 行只是按包名 `@deepseek-ai/dsh-fetch-url` **引用**，它**不会**自动下载/安装。**必须先装好**，否则整个 `dev-flow` 预设无法挂载。
>
> Note: the `fetch-url` row references the package `@deepseek-ai/dsh-fetch-url` **by name**; it never installs or downloads anything. Install it first, or the whole preset cannot mount.

### 2. 安装预设 / Install the preset

```bash
# 确保 DSH_HOME 下存在 .agent-presets 目录
mkdir -p "$DSH_HOME/.agent-presets"
# 把 dev-flow 文件夹拷贝进去 (Windows 下用 $env:DSH_HOME)
cp -r dev-flow "$DSH_HOME/.agent-presets/"
```

结果目录结构：

```
$DSH_HOME/.agent-presets/dev-flow/
├── agent.cordis.yml
├── preset.yml
└── skills/start-project/...
```

`agentPresets` 会**无缓存地**读取该根目录，所以放进去即被识别，无需额外注册命令。

### 3. 使用 / Use it

新建会话 → 在预设选择器里选 **「开发流程」**。

Start a new session and pick **开发流程 / Development Process** in the preset picker.

---

## 验证 / Verify

预设能否挂载（而不是只是被列出）：

```bash
# 用一个会调用 agentPresets.standingKeyFor('dev-flow') 的探针即可；成功 = 挂载通过，失败会报具体某行出错
```

> 若你对"引用未装包导致整包挂载失败"这件事不确定，最简单的验证是：**新建一个会话选「开发流程」**，看它是否能正常启动、工具列表里是否出现 `fetch_url`。

---

## 说明 / Notes

- **预设行只引用、不安装**：`fetch-url` 行引用的是**已安装**的包；`skills/` 里的 `start-project` 随预设**携带**（通过 `skill-filesystem.customSkillDirs` 注册）。
- **可移植性**：本仓库的预设不包含任何绝对路径（`skills/` 用 `baseUrl` 相对解析）。直接分发到其它机器/部署即可；唯一外置要求是接收方的 DSH 里有标准工具包，且装好 `dsh-fetch-url`。
- 如果你想在别处也用这个工具，只需在对应部署先执行上面的第 1 步（`pnpm add`），再放预设。

- **The preset row only references, never installs.** The `fetch-url` row points at an *installed* package; the `start-project` skill **travels** with the preset (registered via `skill-filesystem.customSkillDirs`).
- **Portability.** This repo's preset contains no absolute paths (`skills/` resolves relative to `baseUrl`). Copy it to another machine/deployment as-is; the only external requirement is that the target has the standard tool packages and has installed `dsh-fetch-url`.
- Add this tool anywhere else by running step 1 (`pnpm add`) on that deployment, then placing the preset.

---

## License

本 preset 与其随附内容按 MIT 许可发布（与随附插件一致）。

This preset and its bundled content are released under the MIT License (consistent with the bundled plugin).
