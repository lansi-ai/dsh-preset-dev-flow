# dsh-preset-dev-flow

一个面向代码开发的 **DSH Agent 预设**：让 Agent 按一套"先澄清、再动手、交付出要自检、规则与代码同步"的开发流程来工作。

> English: see [README.en.md](README.en.md)

---

## 这是什么

`dev-flow` 是基于 `standard`（功能完整的编码 Agent）的一个预设。它给 Agent 注入你的开发流程纪律，并随身携带你的 `start-project` 技能。

核心流程（写入 persona，常驻生效）：

- **头脑风暴先行**：接需求先按 5 维框架追问，收敛共识，拿到用户 **Sign-off** 才动手。
- **自动工程交付**（Sign-off 后）：自动检测 `{{RULES_DIR}}`，生成 `docs/prd-and-design.md` + 6 份规则文件（`core-standards` / `architecture` / `active-context` + HTML 看板 / `git-commit-guide` / `workflow`）。
- **编码 SOP**：每任务先做"编码前契约确认"→ 预检 L3/L2/L1 → API-First（单一类型源头 + `ApiResponse<T>` + 开关式 Mock）→ 增量实现（1–3 文件/任务，禁 TODO 堆砌）→ 交付前质量自检链。
- **后置同步**：更新看板 MD+HTML、生成 Conventional Commit 命令、汇报变更。
- **规则自我演进**：技术栈 / 架构 / 流程变化时同步更新对应规则文件。
- **红线**：不硬编码凭据、不逃静态检查、防注入、结构化异常、Schema 强校验、资源自动释放。

---

## 目录结构

```
dsh-preset-dev-flow/
├── README.md                       # 中文说明
├── README.en.md                    # English
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

## 先决条件

- 一个**标准 DSH 部署**：本预设引用的工具（`dsh-tool-pwsh`、`dsh-tool-fs`、`dsh-skill-filesystem`、计划模式、压缩、委托等）都是 `standard` 自带的包；若你的部署没有它们，预设将无法挂载。
- **`@lansi-ai/dsh-fetch-url`**：本预设声明了 `fetch_url` 工具，需先安装（见下）。

---

## 安装

### 1. 安装工具插件（预设 `fetch-url` 行的前置条件）

在本预设使用之前，把插件安装进你的 DSH profile（让它可被解析，但**不会**变成宿主全局）：

```bash
# 在你的 profile 目录里（例如 profiles/web）
cd <your dsh profile>
pnpm add https://github.com/lansi-ai/dsh-fetch-url
# 或者等价地： pnpm add github:lansi-ai/dsh-fetch-url
```

> 说明：预设 `agent.cordis.yml` 里的 `fetch-url` 行只是按包名 `@lansi-ai/dsh-fetch-url` **引用**，它**不会**自动下载/安装。**必须先装好**，否则整个 `dev-flow` 预设无法挂载。

### 2. 安装预设

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

### 3. 使用

新建会话 → 在预设选择器里选 **「开发流程」**。

---

## 验证

最直接的验证：**新建一个会话选「开发流程」**，看它是否能正常启动、工具列表里是否出现 `fetch_url`。若因"引用了未安装的包"导致整包挂载失败，会体现在该会话无法创建。

---

## 说明

- **预设行只引用、不安装**：`fetch-url` 行引用的是**已安装**的包；`skills/` 里的 `start-project` 随预设**携带**（通过 `skill-filesystem.customSkillDirs` 注册）。
- **可移植性**：本仓库的预设不包含任何绝对路径（`skills/` 用 `baseUrl` 相对解析）。直接分发到其它机器/部署即可；唯一外置要求是接收方的 DSH 里有标准工具包，且装好 `dsh-fetch-url`。
- 想在别处也用这个工具：在对应部署先执行上面的第 1 步（`pnpm add`），再放预设。

---

## License

本 preset 与其随附内容按 MIT 许可发布（与随附插件一致）。
