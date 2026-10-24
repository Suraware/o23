---
tags: [playbook, luau]
updated: 2026-10-20
---
# Luau quick rules

## Language hygiene (non-negotiable)
- `task.wait / task.spawn / task.delay` — never the deprecated `wait/spawn/delay`.
- `pcall` every DataStore / Http / remote handler risk; capture `success, err` and log `err`.
- `WaitForChild` **with a timeout** in async flows, then nil-check.
- Types on arguments and returns; `--!strict` on new ModuleScripts.
- Clean up connections and timers on `Destroying` — leaks become ghost logic on dead instances.

## Style (community standard)
- ModuleScript per concern; no require cycles (A->B->A hangs).
- No `Instance.new` in per-frame handlers — pool or reuse.
- Parent last, after setting properties, so listeners fire once.
- Use `Enum.*` instead of magic ints.

## Async patterns
- Server loops do bounded work between `task.wait` ticks; prefer event-wake over polling.
- Wrap fragile async fragments with a promise-ish helper (timeout + retry in one place).

## Runtime gotchas everyone hits
- `require` from ReplicatedStorage is copied per-side: client copy ≠ server copy — no shared mutable state.
- `tonumber`/float precision vs human expectations on money math — round once, centrally.
- `tostring(Vector3)` prints rounded — do not text-parse it; pass the vector.