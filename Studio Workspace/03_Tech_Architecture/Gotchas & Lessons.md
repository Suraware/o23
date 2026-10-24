---
tags: [pipeline, gotchas]
updated: 2026-10-20
---
# Gotchas & Lessons
Every problem we hit, its cause and the fix. **Add to this every time something bites. Read it before building.**

## Studio / MCP
- **Studio left in Play mode** makes edits fail with "Edit datamodel is not available". Stop play first.
- **Re-requiring a ModuleScript returns the cached result.** Destroy and recreate it to rebuild.
- **Newly uploaded textures/meshes render white/blank for a few minutes** while Roblox processes them. Wait before judging.
- **Live collaboration:** if a partner is in the same place, tell them before large edits.
- **Studio MCP tools need a studio_id** — always call `list_roblox_studios` first and cache the id for the session.
- **Console is your ground truth** — `get_console_output` after every scripted change; script errors don't interrupt MCP calls.
- **Play-mode state is fake** — instances created in Play mode vanish on stop. Simulate in Play, build in Edit.

## Luau / runtime
- **`task.wait()`, never `wait()`** — deprecated, less accurate, throttleable by Roblox.
- **Error handler discipline:** wrap network/DataStore calls (`pcall`) and check both return values; silent ``pcall(x)` swallowing hides the error string.
- **`FindFirstChild` vs direct indexing** in async flows: the character may not have loaded; wait with `WaitForChild` **with a timeout** and nil-check.
- **Timers on server need cleanup** — cancel `task.delay`/connections on instance `Destroying` or you leak ghosts that run on deleted data.
- **Vector3 math on client is trusted** — never derive damage, currency or rule outcomes from client-computed vectors.
- **String `tonumber`/`tostring` round-trips DataStore floats fine, but `math.floor(a/b)` on huge numbers loses precision** — use integer math (`math.floor(a / b + 0.5)` for banks).

## Blender / assets (mcp-for-blender)
- **Generated/imported models arrive at arbitrary scale** — size by reported `world_bounding_box`, then drop to ground.
- **Look up shader nodes by type, never by name** — names are localized; `n.type == "BSDF_PRINCIPLED"`.
- **Material colors go on the Principled node inputs** — `material.diffuse_color` only affects viewport, not exports.
- **Blender headless (`-b`) refuses the MCP server on purpose** — run with GUI or `xvfb-run`.
- **The add-on binds 127.0.0.1 only** — remote agents can't reach it. Wire an SSH tunnel if you need it cross-machine.
- **`export_scene` → GLB/FBX is the bridge to Studio MeshParts** — keep scale in metres, origin at feet, +Y up equivalent (Blender Z-up gets converted).

## Tooling (this machine)
- `pkill -f blender` also matches the OpenCode blender MCP process (`mcp-for-blender`) — kill by PID instead.
- Fedora box: no `apt`; flatpak + dnf + user-global npm under `~/.local/opt/npm-global`.

## Tooling (macOS)
- There is no `timeout` command on macOS. Python output redirected to a file is buffered; check output files instead.
## Map building / OBJ
- **Studio can't import .obj through MCP directly** — the Import 3D dialog is UI-locked. Human clicks import once per OBJ; agent does everything after.
- **Blender metres × 3.571 = Roblox studs** (character = 5 studs). Export at the wrong scale and every placement snap lies to you.
- `forward_axis='NEGATIVE_Z', up_axis='Y'` on `wm.obj_export` (Blender 5.x) — wrong flags = models lying on their face in Studio.
- One .obj per hero piece, never the whole map — imports stay editable and streaming stays happy.
## Studio asset uploads (verified 2026-10-20)
- `upload_image` rejects local file paths → **serve the file over localhost http** (`python3 -m http.server 8777`) and pass `http://localhost:8777/x.png`; returns `rbxassetid://<id>` directly.
- `store_image(filePath)` reports 'file unreadable' under Vinegar/Wine even for valid paths — bridge reads on the Wine side. Use the http route.
- A fresh upload shows the dotted-circle placeholder in ImageLabels for minutes while Roblox processes. Don't judge the asset till it clears.

- HUD creation via `execute_luau` (Edit datamodel) verified — ImageLabel + UICorner + GothamBold text all agent-built.