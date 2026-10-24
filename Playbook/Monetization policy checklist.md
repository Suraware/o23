---
tags: [playbook, monetization]
updated: 2026-10-20
---
# Monetization & policy checklist

## Passes & DevProducts
- GamePass = permanent unlock → verify `UserOwnsGamePassAsync` before granting.
- DevProduct = consumable/currency → grant **idempotently**: record the purchased receipt ID before applying effects so replayed grants do not double-pay.
- Always prompt through `MarketplaceService`; never describe a paid promise in gameplay text without the real pass behind it.

## Rules to never break
- No pay-to-win pressure on core loops for free players (fairness > squeezing).
- Paid random rewards: **disclose odds** in the purchase UI.
- Refunds auto-revoke granted items — handle the revocation event, do not fight it.

## Premium payouts
- Engagement-based payouts reward returning, spread-out sessions — design dailies and timers to reward coming back, not infinite grind.

## Store UX
- Sell in context: show offers near the shortfall ('you need 20 more'), not only in a hub menu.
- One click to buy; re-prompt hints on soft-failures.