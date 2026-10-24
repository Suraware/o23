> **Fast path — automated (2026-10-20):** the exact export below is now a tool: `rbxobj` (`~/.local/bin/rbxobj`).
> `rbxobj status` · `rbxobj export --objects "A,B" --name piece` · `rbxobj serve` — bakes in the flags/scale documented here.
> From OpenCode, the slash-command `/rbx-map` drives blender → obj → Studio mechanically with the exact MCP call signatures.
> Doctrine (user's rule): EVERYTHING is designed in Blender; Studio only receives OBJ chunks + scripted cleanup. Whole collections export with `rbxobj export --collection <Name>`.
> Geometry itself: `rbxbuild spec.json --replace` builds whole rooms/furniture from parametric prefab builders (room/wall/stairs/table/chair/shelf/column/tree/rock…) in one socket call — `rbxbuild --list` catalogs them. Blockout first, then export via rbxobj.

---
tags: [playbook, worldbuild, blender]
updated: 2026-10-20
---
# Map building pipeline — Blender `.obj` → Studio

**House rule (owner set 2026-10-20):** agent builds geometry in Blender,
exports **Wavefront .obj** (Roblox Studio imports OBJ natively), and finishes
wiring inside Studio via the Studio MCP. GLB/FBX stays secondary.

## Doctrine in one sentence
- **Blockout in Studio Parts** (execute_luau, 4-stud snap) for 80% of the map.
- **Hero pieces only in Blender** — buildings, landmarks, organic rocks/trees —
one .obj per piece, never one .obj for the whole map.

## Unity of scale (never deviate)
- Roblox character = **5 studs tall**. Roblox stud ≈ 0.28 m, so Blender metres × **3.571** = studs.
- Before export, multiply the piece by 3.571 (or set `global_scale = 3.571` at export).
- Origin = bottom-center of the footprint, so Studio placement lands cleanly.

## Export (mcp-for-blender `execute_code`)
Blender 4.2+/5.x exporter inside loop code:
```
bpy.ops.wm.obj_export(
    filepath="/path/to/project/assets/models/<piece>.obj",
    export_selection_only=True,
    forward_axis='NEGATIVE_Z', up_axis='Y',   # Blender Z-up -> Roblox Y-up
    global_scale=3.571,
    apply_modifiers=True,
    export_triangulated_mesh=True,
)
```
Per-piece constraints, so Studio stays streaming-friendly:
| Rule | Limit |
|---|---|
| one OBJ = one chunk | ≤ 512×512 studs area |
| triangles per piece | ≤ 10k (aim 3–5k) |
| object prefix naming | `BLD_`, `PROP_`, `DET_` |
| draw-call sanity | ≤ 40 unique materials per chunk |

## Studio side (manual one click, agent does the rest)
1. Human/agent with UI: **File → Import 3D... → pick the .obj** (the import
 dialog is UI-locked, so this is the single non-MCP step; everything after is MCP).
2. Agent: `search_game_tree` → find the new MeshPart(s).
3. Agent: `multi_edit` position/rotation; snap via 4-stud grid in code.
4. Agent: `generate_material` / `generate_texture` for surfaces.
5. Agent: `execute_luau` to add collision helpers, convert parts to `Anchored`, group into a Model with `PrimaryPart`.
6. Reuse: publish the piece once (right-click → Save to Roblox) → later spawns via `insert_asset <assetId> parentPath=Workspace.Chunks`.

## Speed path — when Blender is unnecessary
Pure blockmesh zones (halls, ramps, walls) should skip Blender entirely:
`execute_luau` loop spawns `Part`s with perfect box collision — cheaper than
meshes, zero import clicks, and Roblox-native for streaming.

## Verify loop (always)
`start_stop_play true` → run the chunk on foot → `get_console_output` → stop.
Every chunk ships with this pass. No console errors, no stuck player.