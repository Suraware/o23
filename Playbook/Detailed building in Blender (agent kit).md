---
title: Detailed building in Blender (agent kit)
type: playbook
owner: technical-artist
created: 2026-10-21
updated: 2026-10-21
---

# Detailed building in Blender (agent kit)

Roblox detail gets built in Blender, chunk by chunk. Four lanes — pick the lane by cost, not habit:

## Lane 1 — Parametric prefab (fastest)
`rbxbuild spec.json --replace`. Builders: room/wall/floor/stairs/archway/crate/table/chair/shelf/column/tree/rock.
- One spec = whole chunk. Iterate = edit numbers, rerun. `--replace` wipes only its collection.
- Report gives dims (m) + faces per object — fix proportions numerically. Only take `look` when numbers can't settle it (max ~1 look per iteration).

## Lane 2 — `custom` snippets for one-off detail
Same spec file, entry `{"type":"custom","name":"X","code":"..."}`. The snippet is SHORT because helpers are pre-wired:
- `box(nm,w,d,h,loc,m) · cyl(nm,r,h,loc,segs,m) · sphere(nm,r,loc,sub,m) · mat(nm,rgb)`
- `M` (=MT/ML) is one shared material; `parts` is the input list — append everything, the tool joins+grounds+pivots automatically.
- Every entry additionally accepts `bevel` (width), `solidify`, `subdiv`, `shade_smooth`, `at`, `rot`, `color`.
Example cabinet = 9 lines, mirrored twin + arrays for free via `mirror`/`array` entries (host expands them; arrays: `n, offset, rot_step`).

## Lane 3 — Library/AI heroes (for organic or rich-detail props)
- `search_assets` (Poly Haven/Sketchfab/Poly Pizza) — real furniture/rocks/plants, CC0 where possible.
- `generate_3d` — one AI mesh per prompt/image (costs a credit; duplicate it for repeats).
- Position with numbers: get bbox from the import result, `at` it to the target, ground it.

## Lane 4 — Bespoke bpy (last resort)
Long `execute_code` only for things no builder/snippet covers (modifiers stacks, scatters, curves). Bake, cap faces, keep one chunk per file.

## Rules that keep agents fast
1. Never hand-write a table/wall/stairs in bpy — Lane 1/2 exist for that.
2. Numbers before pixels: change spec, rerun `--replace`, read the dims report; `look` to sanity-check once, not per object.
3. One collection per logical chunk (Kitchen, Lobby) — `rbxobj export --collection <Name>` ships it as one OBJ, scale 3.571 = studs.
4. Face budget: Studio import likes chunky geometry. Aim ≤ ~50k faces per chunk OBJ; subdiv sparingly (it multiplies tris ~×4 per level at export since modifiers apply).
5. Blender m → Studio studs: 1 m = 3.571 studs (1 stud = 0.28 m). Room 12×9 m ≈ 43×32 studs, character ≈ 1.4–1.5 m tall.

## Skeleton spec worth memorizing
```json
{"collection": "Kitchen",
 "objects": [
  {"type": "room", "name": "K", "size": [12, 9], "height": 4.2, "thickness": 0.5,
   "door": [3, 2.6, "south", 1]},
  {"type": "custom", "name": "Counter", "at": [3, -3.6, 0], "code": "…"},
  {"type": "array", "n": 4, "offset": [2, 0, 0],
   "item": {"type": "shelf", "name": "CabRow", "size": [1.6, 0.6], "height": 2.2, "shelves": 2, "at": [-3.3, -3.6]}}
 ]}
```
