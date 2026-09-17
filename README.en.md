# dsh-preset-dev-flow

An **agent preset** for the DeepSeek Harness: a disciplined, sign-off-gated *development-process* agent built on the `standard` coding agent.

> 中文说明见 [README.md](README.md)

---

## What it is

`dev-flow` is a preset built on top of the `standard` full coding agent. It injects your development-process discipline into the agent and ships your `start-project` skill alongside it.

Core behavior (injected into the persona, always active):

- **Brainstorm first**: on any request, ask structured questions, converge to a consensus, and require an explicit **Sign-off** before coding.
- **Grade every task A/B/C**: A (contract/architecture/rule changes, milestones, releases) runs the full SOP and gates; B (regular feature, 1–3 files) runs the standard flow; C (small fix ≤3 files, no contract change) is fix + gates + commit, SOP exempt and **no kanban update required**.
- **Auto engineering delivery** (after sign-off, idempotent): when rules are missing it generates `docs/PROJECT-RULES.md` (single entry, §0–§6) + `docs/active-context.md` (MD-only board) + `AGENTS.md` (cross-tool entry); when the rules already exist it skips generation and only tops up a missing entry. A **review gate** (confirm the §0/§1/§2/§6 key assumptions) runs before completion.
- **Single-source rules**: universal discipline (credential injection, type safety, injection prevention, ...) is defined only in the **persona**; project facts and project-specific red lines live in **PROJECT-RULES** — one definition per rule, strictest wins on conflict.
- **Coding SOP**: micro-contract confirmation → pre-flight project rules and kanban → API-First (single type source + `ApiResponse<T>` + switchable Mock) → incremental implementation (1–3 files/task, no TODO stubs) → quality gate.
- **Post-execution sync**: update the MD-only board, emit a Conventional Commit command, report changes.
- **Rule self-evolution**: update the matching section of `PROJECT-RULES.md` when the stack/architecture/process changes.
- **Red lines**: no hardcoded credentials, no type-check evasion, no injection, structured errors, schema validation, resource auto-release.

---

## Layout

```
dsh-preset-dev-flow/
├── README.md                       # Chinese
├── README.en.md                    # English
└── dev-flow/                       # the preset folder (copy it into $DSH_HOME/.agent-presets/)
    ├── agent.cordis.yml            # composition: persona + tools + plan mode + compaction + delegation + skill discovery
    ├── preset.yml                  # display name and description
    └── skills/
        └── start-project/          # the skill shipped with this preset
            ├── SKILL.md
            └── references/         # project-rules / active-context / brainstorming guides
                └── archive/        # core-standards / architecture / git-commit / workflow
                                    # (merged into project-rules-guide; kept for history only)
```

---

## Prerequisites

**Zero prerequisites: just copy `dev-flow` into `$DSH_HOME/.agent-presets/` — no extra package to install.**

- The only requirement is a **standard DSH deployment**. This preset references the standard tool packages (`dsh-tool-pwsh`, `dsh-tool-fs`, `dsh-skill-filesystem`, plan mode, compaction, delegation, ...); if your deployment lacks them the preset will not mount.

---

## Install

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

---

## Use it

Start a new session and pick **开发流程 / Development Process** in the preset picker.

---

## Verify

The simplest check: **start a new session and select 开发流程** and confirm it is created successfully with no extra package installed. Then run one pass of "Phase 1 questions → Sign-off" in an empty project and watch it generate `docs/prd-and-design.md`, `docs/PROJECT-RULES.md`, `docs/active-context.md` and `AGENTS.md`.

---

## Notes

- **Ships with the preset.** The `start-project` skill and its references are registered via `skill-filesystem.customSkillDirs`, so they travel with the preset.
- **Portability.** This repo's preset contains no absolute paths (`skills/` resolves relative to `baseUrl`). Copy it to another machine/deployment as-is; the only external requirement is that the target has the standard tool packages.

---

## License

This preset and its bundled content are released under the MIT License.
