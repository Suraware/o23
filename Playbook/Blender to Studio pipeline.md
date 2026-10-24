---
tags: [playbook, blender]
updated: 2026-10-20
---
# Blender → Roblox Studio pipeline

## Agent toolpath (this machine)
1. Model in live Blender via **mcp-for-blender** (`execute_blender_code` + `look` to see results).
2. Grab stock: `search_assets` / `import_asset` (Poly Haven, Sketchfab, Poly Pizza) or `generate_3d` for one-offs.
3. Export: `export_scene` → **GLB/FBX** into `assets/models/` of the project.
4. Import in Studio as MeshPart; textures via asset upload (`upload_image`) → use returned assetIds.

## Conventions that survive the handoff
- Scale: decide studs-per-metre once; never re-scale mid-project.
- Origin at the feet; apply rotation & scale (Ctrl+A) before export.
- Budget: props 1–8k tris each depending on importance; characters split into meshes per part.
- Materials: Studio only reads standard PBR-ish basics; bake or upload the rest.

## Failure map
| Symptom | Cause | Fix |
|---|---|---|
| mesh renders blank/white | fresh upload still moderating | wait 5–15 min |
| weird overlaps in game | double transforms | apply all + re-export |
| MCP `look` shows garbage | camera far away | use angles mode / refit scene |
| huge scale mismatch | studio import scale mismatch | export at consistent unit scale |
## OBJ-first addendum (owner rule, 2026-10-20)
When the build is **map/level geometry**, export **Wavefront .obj** — Studio imports it natively and network peers sustain it best. Details + exact export flags: [[Playbook/Map building pipeline Blender OBJ to Studio]].
For single decorative items or props, keep GLB/FBX as fallback if .obj loses materials. When in doubt: .obj.
## GUI icon route (verified end-to-end 2026-10-20)
1. Build + render icon in Blender (EEVEE, `view_transform='Standard'`, transparent film, square frame).
2. `serve` it: `python3 -m http.server 8777` in the folder.
3. `upload_image imagePaths=["http://localhost:8777/icon.png"]` → returns `rbxassetid://<id>`.
4. `execute_luau` in Edit → ScreenGui/ImageLabel with that id → `start_stop_play` → `screen_capture` to verify.
Local file paths do NOT work for uploads under Vinegar/Wine — http only. Fresh icons show a placeholder for a few minutes.