# Motion and navigation

## One presentation system

Keep durations, curves, travel, and springs in one semantic source, deriving CSS variables for CSS consumers. Do not maintain parallel literal tables in CSS and JavaScript. Reuse lifecycle owners for press, sheets, notices, snapshots, and navigation across features.

Use transform and opacity for frequent movement. Avoid per-frame React state, repeated layout reads/writes, and uncontrolled layer promotion. Prefer refs/motion values and shared RAF loops. Profile costly blur and snapshots without silently changing approved visuals to make a budget pass.

Common navigation uses a total budget, including internal delays. In Buma that budget is 360ms; a 45ms delay leaves 315ms, not another 360ms. Sequential scenes use 180ms exit + 180ms enter to avoid ghosted overlapping illustrations. Functional timers, progress feedback, deliberate celebrations, and press recipes have separate meanings and budgets.

Do not animate every rerender. Tag real user intent, consume it once, and show restored state directly. Progress numbers and rings should derive from one fractional progress, not two drifting animations. Celebrations respond to committed events, never opening a route or seeing a completed balance.

## Navigation is already real while presentation runs

Keep direction in route/history metadata. Handle PUSH, REPLACE, known POP, and unknown/direct entry explicitly. Do not guess a backward relationship from route names. Commit navigation once; presentation must not replay it from a late callback.

Prioritize specialized transitions over generic route effects. Same-page tabs and query-only overlays should not trigger a generic full-page slide. If native browser navigation already provides a visual transition, suppress the additional app animation. Never cancel browser edge swipes to force the custom effect.

Use View Transitions/snapshots only as progressive enhancement. If unsupported, capture fails, a wait expires, or content is unavailable, reveal the actual destination immediately. In Buma, requesting another capture after an already committed POP stalled in WebKit; returns use the existing entry mechanism instead.

Track presentation generation/ownership. Cleanup from an old effect must not remove attributes, snapshots, or listeners belonging to the next one. New input, rapid navigation, resize, page hiding, `pagehide`, and reduced-motion changes should settle to the correct current state without residual layers.

## Return continuity

Define each return explicitly: destination, history identity, scroll position, focused control, selected card/banner, and draft. Restore only from validated origin metadata, not stale global state or an unchecked query. Query-only overlays must not reset underlying scroll. If manually managing `history.scrollRestoration`, restore its previous value when the owner leaves.

Use stable IDs for selected content. A list index can refer to another card after deletion or reorder. Keep a visible destination/focus fallback when the original item no longer exists.

## Operational feedback

Show success only after the operation's persistence contract succeeds. Ensure repeated taps and animation callbacks cannot duplicate work. Consume transient success feedback once so it does not replay on Back. Keep timers deadline-based where they must survive remounting or backgrounding; an animation timer is not the source of business time.

Do not add haptics as a blanket fix. The current Buma source reviewed for this skill has no shared haptic runtime; historical legacy code is not proof of support. If requested separately, feature-detect the target capability and keep the interaction complete without it.
