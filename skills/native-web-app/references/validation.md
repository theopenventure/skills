# Validation

Use the current project's commands. Do not raise quality baselines or bundle budgets to hide regressions. Documentation-only extraction does not require rebuilding the source app.

## Minimum behavior matrix

| Scenario | Pass condition |
| --- | --- |
| Narrow 320px, reference 393px, larger viewport | No unintended horizontal overflow; long content and last action remain reachable |
| Keyboard open/switch/close, long email, textarea, leading-zero code | Real field and CTA remain accessible; draft, caret, and selection survive; no header jump |
| Form behind a sheet, then close | Original geometry restored; transformed measurements do not persist |
| Multiple scroll-lock owners | Closing one does not unlock another; final release restores styles and intended scroll |
| Sheet drag from top/middle, handle, control, nested scroller | Correct gesture owner; normal scroll preserved; short drag returns; no accidental click |
| Toast drag starting on text/image | Pointer-capture transfer does not freeze or cancel the parent incorrectly |
| Carousel press → sheet → close | Snap and selected identity stay stable |
| Rapid double tap, Back during transition, forward history, direct URL | One action, correct route, no delayed navigation or stale overlay |
| Query-only overlay | Underlying page/scroll preserved |
| Reduced motion initially and toggled live | Correct final state; no residual animation or hidden content |
| Background/foreground, resize, orientation | State preserved; animation resources cleaned; controls usable |
| Failed save/storage, unavailable optional API, failed snapshot | Honest error/fallback, no false success, no lost authoritative state |
| Keyboard-only and assistive technology | Visible focus by default, useful labels, modal ownership, no duplicate announcements |

Test policy/state machines separately from browser geometry. Use trusted browser input for editing and pointer-capture regressions; synthetic dispatch alone can bypass the platform behavior that failed.

## Device and performance evidence

Distinguish code inspection, automated unit tests, browser emulation, trusted desktop input, and physical-phone tests. A synthetic VisualViewport resize tests geometry, not the actual mobile keyboard. Emulation cannot certify safe areas, edge swipes, native photo pickers, HEIC decoding, or overscroll.

On a supported physical phone, record model, OS/browser version, build identifier and local changes, refresh rate, energy/temperature, network, cache, input method, motion preference, and scenario. Test Safari and Chrome on the reference iPhone; include supported Android hardware rather than assuming iPhone results transfer.

Repeat performance scenarios three times after warmup with identical conditions; keep cold load separate. Compare median and worst run. Record screen-recording/probe overhead and inspect browser timelines when available.

RAF callback intervals are not painted FPS. A maximum Event Timing sample is not INP. A local LCP candidate is not production approval. Unavailable metrics are null/unavailable, never zero. A high-refresh display changes the frame budget; investigate regressions against the same device and build conditions.

## Report

State the affected journey, implemented change, automated checks, browser scenarios, physical-device evidence, and outstanding limitations. Keep a device test pending when access is unavailable. Never manufacture a hardware pass from viewport screenshots.
