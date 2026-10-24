---
tags: [playbook, datastore]
updated: 2026-10-20
---
# DataStore safe patterns

## Rules (memorize these four)
1. **Never trust client ids** — all keys are server-composed: `"profile_" .. userId`.
2. **GetAsync on join, save on leave + autosave** — autosave every ~60 s *and* save on `PlayerRemoving`; wrap **every** call in `pcall` + 3 retries with 5 s backoff.
3. **One key prefix per data type** — `profile_`, `inventory_`, `settings_`. Never one mega-key.
4. **Version the schema** — `{ schemaVersion = 1, coins = 0 }`; a migration function applies defaults for missing fields, upgrades `schemaVersion` forward-only.

## Session-safe writes
- Session-lock via MemoryStore TTL (or accept last-write-wins in single-server games).
- On write conflicts: re-read after ~5 s and reconcile **fieldwise** — never blind-overwrite.
- Keep a `ledger` of currency changes (amount, reason, ts) — turns exploit forensics into a one-query job.

## Anti-patterns (will bite you)
- Mixed-type table keys (mixed int/string keys do not translate cleanly through JSON round-trips).
- `UpdateAsync` inside a hot loop — batch budget blowout; throttle writes.
- Saving in `PlayerRemoving` without `pcall` → data loss under server shutdown; also bind `BindToClose` for shutdown saves.
- Trusting `Players.LocalPlayer` **on the server** — it is always nil there; that nil check failing silently is a classic data-wipe bug.