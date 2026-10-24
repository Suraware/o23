---
title: HUD design patterns
type: pattern
category: patterns
subcategory: ui
owner: ui-programmer
status: draft
created: 2026-10-21
updated: 2026-10-21
sources:
  - .claude/agents/ui-programmer.md
  - wiki/patterns/responsive-design.md
---
# HUD design patterns

Composition rules for the persistent play-view layer (health, currency, cooldowns, prompts). Complements [[patterns/ui-framework-comparison]] (which UI lib) — this page is *what good HUDs are made of*.

## Layout skeleton
- Anchor-based placement (`AnchorPoint` + `fromScale`), never absolute pixels: bottom-left status, top-right resources, bottom-center action, top-center objectives & boss bars.
- One SafeArea wrapper honoring `GuiService:GetGuiInset()` so notches/touch bars never clip content.
- Prefer `UIListLayout` stacking over hand-placed rows — new widget = insert, not reposition.

## Status widgets
- Coin/resource pill: container Frame + icon ImageLabel + number TextLabel; **tween the number, not the layout**. Verified agent-built example lives in the project HUD (coin icon `rbxassetid://95319549853429`).
- Progress bars: backing Frame + fill child with `UISizeConstraint`; ghost-fill second layer trails the real fill for perceived smoothness.
- Cooldowns: radial via `RadialImage`/pie texture or sweep gradient; text countdown updates on integer change only.

## Interaction
- Touch targets ≥ 44 px; no hover-only affordances (touch has none) — pair hover styles with always-visible activation.
- Popups spawn with one 'primary' guaranteed action; two max high-priority popups — drop or queue the rest (see [[concepts/rate-limiting]] for the server side of prompt spam).
- Tween entry/exit 0.15–0.25 s with easing; instant UI feels glitchy, slow UI feels laggy.

## Traps
- Text re-formatting every frame allocates strings per client — update only on value change.
- Rotating/z-animating translucent labels forces re-sorting — bake gradients instead of layered alpha.
- Player-scale UI (`UIScale`) for accessibility — one knob, applied at the top container.