---
tags: [playbook, performance]
updated: 2026-10-20
---
# Performance — mobile FPS survival

Rough budget: aim 30+ FPS on mid Android. Pain usually starts past ~2000 parts on screen or hundreds of active particles/lights.

## The four killers, in order
1. **Instance count / draw calls** — merge static decor, hide with StreamingEnabled.
2. **Particles, trails, beams** — cap rate + lifetime; they murder thermal budget.
3. **Per-frame script work** — no per-frame `Instance.new`; pool, throttle, early-exit Heartbeat loops.
4. **Lighting** — Future + shadowed lights on mobile needs care; avoid volumetric fog by default.

## Habits
- Profile in Studio (performance tab), then re-check as a mobile device class via `get_device_type`-ish heuristics: adapter plan for 4 GB RAM.
- Prefer CFrame-driven motion for decorative movers (no Humanoid physics unless needed).
- Sparse collision groups; guard `Touched` against wild spam at the door.

## Streaming
- StreamingEnabled: yes by default for a live game sized above a baseplate.
- Client scripts that index far-away instances must tolerate nil — use `WaitForChild` timeouts or move those checks server-side.