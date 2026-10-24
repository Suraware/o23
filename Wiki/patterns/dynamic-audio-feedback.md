---
title: Dynamic audio feedback
type: pattern
category: patterns
subcategory: audio
owner: sound-designer
status: draft
created: 2026-10-21
updated: 2026-10-21
sources:
  - wiki/raw/roblox-creator-docs/services/Sound.md
  - wiki/raw/roblox-creator-docs/services/SoundGroup.md
---
# Dynamic audio feedback

The mix that makes a game feel alive: state-driven music, event juice, and buses that keep them out of each other's way.

## Music state machine
- States: menu / safe / tension / combat / victory — one `MusicDirector` ModuleScript owns all track lifecycle; ad-hoc music playback is forbidden.
- Transition = fade out (1.5–3 s) → fade in. Align to bar boundaries when tracks loop cleanly; never hard-switch mid-phrase.
- Duck priority audio: on voice/boss roar, tween music bus volume −35% for the clip's duration, restore on `Ended`.

## Event juice
- Reward blips: pitch variance ±5% (`PlaybackSpeed`), layered timbre (chime + transient), and per-asset rate limiting — 50 identical coin dings/minute is noise, apply cooldown or pitch ladder.
- Distance: `RollOffMin/MaxDistance` curves sell space better than raw volume; near/mid/far layering for ranged combat.
- Stagger simultaneous sounds by 60–80 ms when they share a bus (coin + confirm).

## The game-wide mix hierarchy
- Bus order, always-on Compressor threshold on the master: **voice > ui > sfx > music > ambience**.
- Master stays at unity — if the game 'needs' >100% master, the bus levels are wrong, not the player's speakers.
- Validate mixes on cheap speakers too; tin-laptop survival of the sfx bus is a real shipping gate.

## Traps
- `PlaybackLoudness` metering only works on the sound-owning client — visualizers live client-side.
- Streamed audio (`AssetService`) for voice: mind sampling ratelimit and codec size on mobile.
- SoundService requires client context for spatial parameters — server can verify instances, not hear them.