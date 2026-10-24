---
tags: [playbook, studio]
updated: 2026-10-20
---
# Studio MCP workflow

The agent's hands in Studio — 28 tools. The reliable loop:

## Boot sequence (every session)
1. `list_roblox_studios` → grab `studio_id` (cache it for the session).
2. `get_studio_state` → confirm Play/Edit state and available services.
3. `script_grep` before `script_read` before edits — never modify blind.

## Daily loop
- **search → read → edit → validate → console** — e.g. `script_grep "DataStore"` → `script_read` the hit → edit → `get_console_output`.
- **Playtest safely:** `start_stop_play true` → exercise → `get_console_output` → `start_stop_play false`.
- Save the place (Ctrl+S) before closing; Play-mode instances vanish on stop — simulate in Play, build in Edit.

## Failure map
| Symptom | Cause | Fix |
|---|---|---|
| 'Edit datamodel is not available' | Studio is in Play mode | stop play first |
| MCP edits silently lost | unsaved place state | Ctrl+S / track place file |
| 'no studio instances' | Studio closed / Vinegar not running | open Studio via Vinegar |
| script search returns nothing | filter or no LocalScripts only | widen the query |

## Ground rules
- Prefer **files + Rojo** (`rojo serve` + Studio plugin) over cooking scripts in Studio directly — greppable, diffable, portable.
- Every scripted change should be something you could also do by hand — keep the human path familiar.