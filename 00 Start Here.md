# ⬒ Roblox Dev Vault — Start Here

> Vault owned by the OpenCode Roblox studio workflow. Agents reach this vault
> through the `obsidian` MCP tools (search / read / create / update notes only).
> Humans: open Obsidian → this vault is pre-registered as the default.

## Knowledge base → [[Wiki/index]]

778 notes imported from **CodePhobiia/claude-roblox-game-studio** (MIT):
- **Wiki/** — Luau + Roblox wiki (services, concepts, anti-patterns, exploits, performance, monetization, studio workflows). Start at [[Wiki/index]], health-check at [[Wiki/SCHEMA]].
- **Design/** — GDD, economy, levels, narrative templates: [[Design/COLLABORATIVE-DESIGN-PRINCIPLE]] · [[Design/WORKFLOW-GUIDE]]
- **StudioDocs/** — architecture guide, Luau style guide, coordination rules.

## Conventions

- New design docs → `Design/` (use the templates there).
- Anything Luau/Roblox reference → `Wiki/<section>/` and update [[Wiki/log]].
- Raw third-party captures stay immutable under `Wiki/raw/`.
- Do **not** pull in Roblox outside assets or create stray nested vaults.

## Pipeline reminders

- Blender MCP (`mcp-for-blender`) models → `export_scene` GLB/FBX → Studio import.
- Studio MCP (`roblox_studio`) → live testing, scripts, console.


## Studio Workspace (living docs, copied from roblox-claude-skills)
- [[Studio Workspace/00 Home|Home]] → [[Studio Workspace/01_Vision/Game Vision|Vision]] → [[Studio Workspace/03_Tech_Architecture/Gotchas & Lessons|Gotchas & Lessons]] → [[Studio Workspace/05_Production/Roadmap|Roadmap]]
- Fill [[Studio Workspace/00_Inbox/Inbox|Inbox]] with anything unsorted.

## Playbook — how-to guides (agent-curated, practical)
1. [[Playbook/Studio MCP workflow]]
2. [[Playbook/DataStore safe patterns]]
3. [[Playbook/Remotes and anti-exploit]]
4. [[Playbook/Performance mobile FPS]]
5. [[Playbook/Luau quick rules]]
6. [[Playbook/Blender to Studio pipeline]]
7. [[Playbook/Monetization policy checklist]]
8. [[Playbook/Release checklist]]

Deep reference stays in [[Wiki/index]] — Playbook = "do this tomorrow morning" level.
## Map building & world design (added 2026-10-20)
- [[Playbook/Map building pipeline Blender OBJ to Studio]] — agent rule: Blender → .obj → Studio, exact export flags, per-chunk budgets.
- [[Playbook/Level design doctrine]] — zone graph, sightline rule, mobile/streaming guardrails, acceptance checklist.

House rule: agent geometry that becomes level content goes out as **.obj** (Studio-native import), not GLB/FBX.