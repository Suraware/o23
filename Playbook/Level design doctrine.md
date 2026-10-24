---
tags: [playbook, level-design]
updated: 2026-10-20
---
# Level design doctrine

## Player flow (first-person-of-time test)
- **Time-to-first-fun < 60 s** from spawn — an action, a view, or a pickup before minute one.
- **Content loop 60–90 s** — walk, interact, combat, return; then loop again with escalation.
- **Spawn → action < 8 s** — no menus, no empty corridors in between.
- **No dead ends > 2** — every cul-de-sac needs a reward or it teaches players to leave.

## Zone graph
- Sketch zones as a graph with one-to-three-touch edges; **radiate from a central hub** for retention (players return to shop/social point).
- Golden path must be readable **without signs**: light, color, height, landmark.
- Junction rule: from every junction, **exactly one landmark visible** — if two, players hesitate; if none, they get lost.

## Props & landmarks pacing
- Landmark every ~30 studs of walking; micro-reward every ~10 s.
- Elevate early successes: first zone teaches one verb (jump / push / collect) and leaves.
- Skybox variation per zone to signal region change (players feel progress).

## Mobile + streaming guardrails
- Verticality ≤ 80 studs per zone before refresh; players quit climbing they can't see.
- Chunked zones ≤ 512 studs (see [[Playbook/Map building pipeline Blender OBJ to Studio]]).
- LOD plan for every hero piece (distance-based part visibility, not just mesh LOD).
- Never more than ~5 jarring style/mood changes in a heartbeat — motion-sickness risk on mobile.

## Level acceptance (agent checklist)
1. Walk the golden path on foot in Play mode — is fun intact? `run` loop via Studio MCP.
2. Jump-spam test: can players skip content by jumping around intended obstacles? If yes, fix geometry-only.
3. Sightline audit at every junction (one landmark visible).
4. Console clean after the whole loop.
5. Revisit: leave the map, come back — does zone recognition come back instantly?