---
tags: [playbook, release]
updated: 2026-10-20
---
# Release checklist

## Code
- [ ] lint + format clean (`selene`, `stylua`); no console errors on a clean boot.
- [ ] Every remote validated & rate-limited.
- [ ] `BindToClose` save path tested (server shutdown under load).
- [ ] DataStore migration verified against real old profiles.

## Studio
- [ ] No debug parts or placeholder props in Workspace.
- [ ] StarterPlayer camera + mobile controls sane; thumbs zones ≤ 4.
- [ ] Place loads < 5 s on a mid-range phone (streaming on, lighting sane).

## Publish
- [ ] Changelog entry written (this vault, Studio Workspace/05_Production).
- [ ] Staged rollout: internal place → friends-only → public.
- [ ] 60 min watch window post-live: console errors, player count, economy rate.
- [ ] Rollback plan: last-known-good version tagged in Studio.

## Analytics
- [ ] First-session funnel instrumented: first input, first success, first return-hook.
- [ ] Economy monitored 24 h: currency-per-hour per cohort stable.