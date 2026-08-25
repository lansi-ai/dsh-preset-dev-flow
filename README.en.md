# dsh-preset-dev-flow

An **agent preset** for the DeepSeek Harness: a disciplined, sign-off-gated *development-process* agent built on the `standard` coding agent.

> 中文说明见 [README.md](README.md)

---

## What it is

`dev-flow` is a preset built on top of the `standard` full coding agent. It injects your development-process discipline into the agent and ships your `start-project` skill alongside it.

Core behavior (injected into the persona, always active):

- **Brainstorm first**: on any request, ask structured questions, converge to a consensus, and require an explicit **Sign-off** before coding.
- **Auto engineering delivery** (after sign-off): detect `{{RULES_DIR}}` and generate `docs/prd-and-design.md` plus 6 rule files (`core-standards`, `architecture`, `active-context` + HTML board, `git-commit-guide`, `workflow`).
- **Coding SOP**: micro-contract confirmation → pre-flight L3/L2/L1 → API-First (single type source + `ApiResponse<T>` + switchable Mock) → incremental implementation (1–3 files/task, no TODO stubs) → quality gate.
- **Post-execution sync**: update the MD+HTML board, emit a Conventional Commit command, report changes.
- **Rule self-evolution**: update the matching rule file when the stack/architecture/process changes.
- **Red lines**: no hardcoded credentials, no type-check evasion, no injection, structured errors, schema validation, resource auto-release.

---

## Layout

```
dsh-preset-dev-flow/
├── README.md                       # Chinese
├── README.en.md                    # English
└── dev-flow/                       # the preset folder (copy it into $DSH_HOME/.agent-presets/)
    ├── agent.cordis.yml            # composition: persona + tools + plan mode + compaction + delegation + skill discovery + fetch_url
    ├── preset.yml                  # display name and description
    └── skills/
        └── start-project/          # the skill shipped with this preset
            ├── SKILL.md
            └── references/         # brainstorming / core-standards / architecture /
                                    # active-context / git-commit / workflow guides
```

---

## Prerequisites

- A **standard DSH deployment**. This preset references the standard tool packages (`dsh-tool-pwsh`, `dsh-tool-fs`, `dsh-skill-filesystem`, plan mode, compaction, delegation, ...); if your deployment lacks them the preset will not mount.
- **`@deepseek-ai/dsh-fetch-url`**: this preset declares the `fetch_url` tool, so it must be installed first (see below).

---

## Install

### 1. Install the tool plugin (prerequisite for the `fetch-url` row)

Before using this preset, install the plugin into your DSH profile so it is resolvable (but it will **not** become a host-global bundle):

```bash
# In your DSH profile directory (e.g. profiles/web)
cd <your dsh profile>
pnpm add https://github.com/lansi-ai/dsh-fetch-url
# or equivalently: pnpm add github:lansi-ai/dsh-fetch-url
```

> Note: the `fetch-url` row in `agent.cordis.yml` references the package `@deepseek-ai/dsh-fetch-url` **by name**; it never installs or downloads anything. Install it first, or the whole preset cannot mount.

### 2. Install the preset

```bash
# Make sure .agent-presets exists under DSH_HOME
mkdir -p "$DSH_HOME/.agent-presets"
# Copy the dev-flow folder into it (Windows: use $env:DSH_HOME)
cp -r dev-flow "$DSH_HOME/.agent-presets/"
```

Resulting structure:

```
$DSH_HOME/.agent-presets/dev-flow/
├── agent.cordis.yml
├── preset.yml
└── skills/start-project/...
```

`agentPresets` reads that root **unmemoized**, so the preset is recognized as soon as it is placed — no extra registration command.

### 3. Use it

Start a new session and pick **开发流程 / Development Process** in the preset picker.

---

## Verify

The simplest check: **start a new session and select 开发流程**. If it starts and `fetch_url` appears in the tool list, the preset mounted correctly. If the preset references an uninstalled package, session creation will fail.

---

## Notes

- **The preset row only references, never installs.** The `fetch-url` row points at an *installed* package; the `start-project` skill **travels** with the preset (registered via `skill-filesystem.customSkillDirs`).
- **Portability.** This repo's preset contains no absolute paths (`skills/` resolves relative to `baseUrl`). Copy it to another machine/deployment as-is; the only external requirement is that the target has the standard tool packages and has installed `dsh-fetch-url`.
- Add this tool anywhere else by running step 1 (`pnpm add`) on that deployment, then placing the preset.

---

## License

This preset and its bundled content are released under the MIT License (consistent with the bundled plugin).
