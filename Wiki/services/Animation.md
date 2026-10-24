---
title: Animation
type: service
category: services
subcategory: animation
owner: technical-artist
status: complete
created: 2026-10-21
updated: 2026-10-21
sources:
  - wiki/raw/roblox-creator-docs/services/Animation.md
  - wiki/raw/roblox-creator-docs/services/AnimationTrack.md
  - wiki/raw/roblox-creator-docs/services/Animator.md
---
# Animation

Asset object for a rig animation. The playback stack: `Animation` (asset) → `AnimationTrack` (via `Animator:LoadAnimation`) → `Animator` (lives inside Humanoid / AnimationController).

## Replication rules (the load-bearing trap)
- **Load and play on the side that owns the rig.**
  - NPC / server-owned rigs: server loads and plays → replicates to clients.
  - Player characters: *that client* loads and plays its own Humanoid's Animator. Server-side `LoadAnimation` on a client rig does not animate what other players see.
- Always use `Humanoid.Animator`; `Humanoid:LoadAnimation` is deprecated.
- Cross-side chase: if the server must know (hit confirmation), mirror the animation on each client from an event payload.

## Track control
- `track.Priority` resolves overlapping-joint fights: `Action > Movement > Idle`.
- `track:Play(fadeTime, weight, speed)` — fade `0.1–0.2` s on humanoids; hard cuts look robotic.
- Cross-fade = `track.WeightCurrent` ramp by playing two tracks simultaneously with changing weight.
- `Speed < 0` rewinds; `Looped` never fires `Ended` — loop control needs an external trigger.
- `track:AdjustSpeed()` mid-flight for reaction effects (hit slow-mo via 0.4 speed pulses).

## Agent checklist
- Preload: `ContentProvider:PreloadAsync({animAsset})` before first play — first-play hitch otherwise.
- Guard rig lifecycle: NPC destroyed mid-track throws `Unable to cast value to array`-style track errors in console — disconnect per-fire connections on `Destroying`.
- Tool animations: verify grip — animation space vs `Tool.Grip` mismatch floats the weapon.
- See [[patterns/state-machine-pattern]] for track orchestration (move/jump/attack states).