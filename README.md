# dsh-preset-dev-flow

一个面向代码开发的 **DSH Agent 预设**：让 Agent 按一套"先澄清、再动手、交付出要自检、规则与代码同步"的开发流程来工作。

> English: see [README.en.md](README.en.md)

---

## 这是什么

`dev-flow` 是基于 `standard`（功能完整的编码 Agent）的一个预设。它给 Agent 注入你的开发流程纪律，并随身携带你的 `start-project` 技能。

核心流程（写入 persona，常驻生效）：

- **头脑风暴先行**：接需求先按 5 维框架追问，收敛共识，拿到用户 **Sign-off** 才动手。
- **任务分级 A/B/C**：A（契约/架构/规则变更、里程碑、发版）走全 SOP + 全门禁；B（常规功能 1–3 文件）走标准流程；C（小修 ≤3 文件、无契约变更）只做「修 + 门禁 + 提交」，SOP 豁免且**不强制更新看板**。
- **自动工程交付**（Sign-off 后，幂等）：缺规则时生成 `docs/PROJECT-RULES.md`（单入口，§0–§6）+ `docs/active-context.md`（仅 MD 看板）+ `AGENTS.md`（跨工具入口）；规则已就位则跳过，缺入口只顺手补齐。生成后先过**审阅闸门**（确认 §0/§1/§2/§6 关键假设）再收官。
- **规则单源分工**：通用纪律（凭据注入、类型安全、防注入…）只在 **persona** 定义；项目事实与专属红线在 **PROJECT-RULES** 定义，同物一处、冲突取严。
- **编码 SOP**：每任务先做"编码前契约确认"→ 预检项目规则与看板 → API-First（单一类型源头 + `ApiResponse<T>` + 开关式 Mock）→ 增量实现（1–3 文件/任务，禁 TODO 堆砌）→ 交付前质量自检链。
- **后置同步**：更新看板（仅 MD）、生成 Conventional Commit 命令、汇报变更。
- **规则自我演进**：技术栈 / 架构 / 流程变化时同步更新 `PROJECT-RULES.md` 的对应节。
- **红线**：不硬编码凭据、不逃静态检查、防注入、结构化异常、Schema 强校验、资源自动释放。

---

## 目录结构

```
dsh-preset-dev-flow/
├── README.md                       # 中文说明
├── README.en.md                    # English
└── dev-flow/                       # 预设文件夹（复制进 $DSH_HOME/.agent-presets/ 即可）
    ├── agent.cordis.yml            # 预设组成：persona + 工具 + 计划模式 + 压缩 + 委托 + skill 发现根
    ├── preset.yml                  # 显示名「开发流程」+ 描述
    └── skills/
        └── start-project/          # 随预设携带的技能
            ├── SKILL.md
            └── references/         # project-rules / active-context / brainstorming 指南
                └── archive/        # core-standards / architecture / git-commit / workflow
                                    # （已并入 project-rules-guide，仅作历史参考）
```

---

## 先决条件

**零先决条件：把 `dev-flow` 拷进 `$DSH_HOME/.agent-presets/` 即可，无需安装任何额外包。**

- 唯一要求是一个**标准 DSH 部署**：本预设引用的工具（`dsh-tool-pwsh`、`dsh-tool-fs`、`dsh-skill-filesystem`、计划模式、压缩、委托等）都是 `standard` 自带的包；若你的部署没有它们，预设将无法挂载。

---

## 安装

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

---

## 使用

新建会话 → 在预设选择器里选 **「开发流程」**。

---

## 验证

最直接的验证：**新建一个会话选「开发流程」**，确认它能正常创建、且无需安装任何额外包。随后在一个空项目里走一遍「阶段 1 追问 → Sign-off」，即可看到它生成 `docs/prd-and-design.md`、`docs/PROJECT-RULES.md`、`docs/active-context.md` 与 `AGENTS.md`。

---

## 说明

- **随预设携带**：`skills/` 里的 `start-project` 及其 references 通过 `skill-filesystem.customSkillDirs` 注册，跟着预设走。
- **可移植性**：本仓库的预设不包含任何绝对路径（`skills/` 用 `baseUrl` 相对解析）。直接分发到其它机器/部署即可；唯一外置要求是接收方的 DSH 里有标准工具包。

---

## License

本 preset 与其随附内容按 MIT 许可发布。
