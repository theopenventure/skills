# Buma profile — explicit opt-in

Extracted 16 November 2026 from the Buma working tree. Use these settings to reproduce the chosen Buma behavior, not as universal mobile defaults. Preserve a target project's newer explicit decisions.

To port these settings and the shell behavior behind them, follow the [implementation checklist](buma-implementation.md), which also has portable code.

## Settings

| Area | Observed setting | Application |
| --- | --- | --- |
| Common navigation | 360ms total | Include all delays within the budget |
| Sequential transition | 180ms exit + 180ms entry | Prevent doubled artwork/copy |
| Navigation easing | cubic-bezier(0.23, 1, 0.32, 1) | Shared CSS/WAAPI/RAF progress |
| Exit easing | cubic-bezier(0.4, 0, 1, 1) | Accelerating exit where the recipe calls for it |
| Page/local travel | 24px / 8px | Semantic defaults, not mandatory movement |
| Local feedback | 200ms, scale 0.97 | Distinct from CTA press tokens |
| Popover/state | 180ms / 220ms | Do not collapse into navigation timing |
| Drawer button close/open | 360ms | Independent of sheet height |
| Drawer release | Frequencies 24/s return, 30/s close | Velocity-preserving damped settle, max 360ms |
| Drawer settle | 0.5px tolerance after 60ms | End exactly at rest |
| Drawer backdrop | Scale 0.9166, blur 4px, final opacity 0.4 | Same progress as sheet |
| Drawer geometry | Context top 32px, sheet top 75px plus applicable safe inset | Preserve ownership of safe areas |
| Drawer drag | 5px intent, 20px protected side edges | Do not compete with browser Back |
| Drawer dismissal | min(150px, 24% height), or 28px + 0.6px/ms downward velocity | Reject upward reversal; sample recent 100ms |
| Mobile composition | Max width 480px, gutters 24px | Do not force on desktop products |
| Input baseline | 16px | Keep native caret/selection |
| Hit target baseline | 44px | Can exceed visible icon size |
| Progress | Current source: sparse 1400ms, full 900ms | Older prose says 1350ms; inspect current token/function before copying |

Centralize these settings; never scatter them as literals across screens. Detailed branded trajectories and illustrations are not required to make another product feel native.

## Deliberate product exceptions

- **Zoom:** Buma uses `touch-action: pan-x pan-y` on app/dialog surfaces plus scoped WebKit gesture cancellation. Generic mode must preserve pinch zoom. Do not add `user-scalable=no`, `maximum-scale=1`, or blanket touchmove cancellation. The crop surface remains independently zoomable.
- **Selection:** Buma blocks long-press selection on display text, retaining native selection/callouts in inputs, textareas, and contenteditable regions. Other products should keep meaningful content copyable.
- **Orientation:** Buma uses a portrait notice on coarse-pointer devices, keeps routes mounted/inert underneath, and shares the scroll lock. Use actual orientation signals; keyboard height is not orientation. This does not lock the operating system.
- **Theme:** The recorded Buma choice is `color-scheme: only light` with matching metadata, maintaining the approved palette. Do not introduce this restriction into a product with dark-mode support.
- **Focus appearance:** Current Buma CSS removes focus outlines globally while retaining focus behavior. Record this as a product exception with accessibility cost, not a reusable default. Keep visible keyboard focus in generic mode; preserving this exception does not establish accessibility compliance.
- **Autofill:** Earlier records approved suggestions; later access flows reference `manualEntryProps`. Inspect the current field policy rather than restoring old autocomplete attributes unconditionally. Generic mode keeps appropriate native autofill.
- **Browser refresh:** Buma's CSS containment needed a scoped compatibility fallback. A separate wallet pull gesture is a product interaction; it does not authorize browser reloads or backend refresh behavior.

## Evidence status

The October 29 device log includes positive functional reports for wallet/tour, drawer dismissal, navigation interruption, and an initial keyboard pass. It does not validate every later build or every scenario. The physical matrix remains open; reduced motion was omitted from that round. Historical test counts are not test results for a new application of this skill.
