# Viewport and input

## Keep one coordinate system

Use `viewport-fit=cover` where the app intentionally draws edge to edge, and reserve actual `env(safe-area-inset-*)` values. Do not hardcode the status bar from a design mockup. Add an inset once at its owner; nested overlays must not count it again.

Choose height units by purpose: `svh` for a composition that should remain stable as browser bars change, `dvh` for a surface intended to follow the dynamic viewport. Neither alone proves correct keyboard behavior. Avoid blanket replacement of all heights and avoid assuming `100vh` equals the visible area.

For fixed form journeys that need persistent chrome:

1. Reuse a shared form shell. Read `visualViewport.height` and `offsetTop`, falling back to `innerHeight` and zero when unavailable.
2. Position the whole shell using its height and top. Avoid a transform on the shell when it would change fixed descendants' coordinate space.
3. Reserve header/title/actions; give the fields a `min-height: 0` internal scroller. Keep the title and actions out of the scrolling region unless the approved design has explicit overlay edges.
4. Let whitespace and decorative artwork yield before reducing type or touch targets. Scroll only when the content cannot fit. Never add the full keyboard height as document padding or repeatedly call `window.scrollBy` to chase focus.
5. Reveal the edited field within the usable scroller bounds. Include reserved chrome via scroll padding. Avoid fractional-pixel oscillation.
6. Observe viewport resize/scroll, focus, input, relevant element size, window resize, and page restoration. Coalesce updates with RAF; remove listeners and disconnect observers on cleanup.
7. Restore internal scroll only after the full viewport returns, not midway through the keyboard-closing animation.

A height difference threshold is a layout heuristic, not a reliable keyboard API: browser bars, pinch zoom, and small windows can also resize the viewport. Do not use it to infer orientation or disable user controls. Preserve page zoom in general-purpose apps; account for `visualViewport.scale` when adapting a fixed shell.

Measure reserved space with untransformed layout sizes (`offsetHeight`) when the page can be scaled behind a sheet. A transformed `getBoundingClientRect` height persisted into layout can create a jump after closing. Use bounding rectangles for comparing positions only when both measurements share the same coordinate system.

## Preserve native text entry

| Purpose | Baseline attributes |
| --- | --- |
| Email | `type="email"`, `autoComplete="email"`, `autoCapitalize="none"`, `autoCorrect="off"` |
| Phone | `type="tel"`, `autoComplete="tel"` |
| Verification code | `type="text"`, `inputMode="numeric"`; `autoComplete="one-time-code"` if that product supports it |
| Name | `autoComplete="name"` or `given-name`, matching the requested data |
| Day/month | Text with numeric input mode and the appropriate birthday autocomplete token |

Use at least 16px input text as the baseline against iOS focus zoom. Do not compensate with `maximum-scale=1` or `user-scalable=no`.

Codes are strings: preserve leading zeros, paste, correction, and selection. Use one native field with a visual digit presentation when needed, rather than forcing focus across artificial digit inputs. Read code length from product rules, never a copied demo constant.

`enterKeyHint` changes the offered keyboard label; implement the intended action separately. In the Buma pattern, Next focuses the next field and Done ends editing; submission belongs to the CTA. Preserve keyboard activation of that CTA. Code verification may have its own explicit complete-code rule; do not infer it from Enter or restored values.

Keep draft restoration silent: restoring a field is not a fresh user edit. Do not lowercase or otherwise mutate user input merely because autocapitalization is disabled. Apply normalization only at an explicit domain boundary.

## Optional animated typing

Use an inert visual layer over the real editor, never replace native editing. Animate inserted graphemes only. Cancel on deletion, selection, blur, scroll, resize, composition, reduced motion, or teardown. Keep long pastes, bidi text, incompatible glyph geometry, and horizontally clipped fields native. Buma caps animated paste at 32 graphemes; treat this as a tuning limit.

Measure only the changed range and nearby text, not an entire long textarea per frame. Test real browser input: a microtask can run between an input listener and a delegated React commit. Do not expire edit intent before the commit can consume it; clear any deferred expiration on the next edit, composition, or teardown. Synthetic events wrapped in synchronous flushing can hide this failure.
