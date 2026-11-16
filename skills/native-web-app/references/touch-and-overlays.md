# Touch and overlays

## Press without hijacking activation

Gate hover with `(hover: hover) and (pointer: fine)`. Keep keyboard focus visible. Use semantic buttons/links and adequate hit areas (Buma's baseline is 44px). Suppress tap highlight only when custom feedback provides a clear replacement.

Let `:active` own press feedback where possible, including keyboard Space and cancellation during scrolling. Compose a separate `scale` or nested surface with entry transforms. Exclude disabled/busy controls and avoid scaling both a parent and its active child. Keep color feedback when reduced motion removes scale.

Apply `user-select: none` and touch-callout suppression to controls, not all content by default. Preserve editable fields and their native selection menus. Disable native image dragging only where it interferes with intended interactions.

## Native scroll first

Use native overflow and scroll snap for lists and carousels. Place `scroll-snap-align` on a stable, unscaled slot; animate the inner pressable content. Scaling the snap owner caused Buma's carousel to move when opening a drawer.

Choose `touch-action` for the actual owner: native horizontal overflow must retain horizontal browser panning; a custom horizontal pointer drag can reserve horizontal motion while allowing native vertical panning. Do not copy `pan-y` onto every native carousel. Use `touch-action: none` only on a deliberately custom surface such as a crop editor, with equivalent accessible controls.

Contain scroll chaining using CSS first. Introduce non-passive touch cancellation only for an observed compatibility failure or recognized custom gesture, scoped to that surface. Do not suppress all touch movement on the document.

Buma's browser-refresh fallback records one touch, inspects scrollable ancestors, and cancels only cancelable, predominantly downward movement while all relevant scrollers are at the top. It excludes editing and crop surfaces, horizontal/upward/multitouch gestures, and cleans up all listeners. This does not guarantee suppression on iOS once the browser owns a noncancelable gesture. Retest from the top and from 20–40px below it. A product's own pull interaction is separate from a browser reload; give it an explicit owner and an accessible alternative.

## Share scroll locks

Use one reference-counted document lock for forms, dialogs, and orientation notices. Save scroll position and exact inline values/priorities only on the first acquisition; return an idempotent release function. Restore styles and scroll only after the last owner releases. A fixed body can report scrollY=0: expose the suspended position to restoration code. If navigation changes the intended position while locked, update the saved position too.

## Bottom sheets

Reuse a native dialog or the existing accessible modal primitive, with a label, contained focus, inert background, Escape/close controls, and focus restoration. Keep the page mounted when continuity requires it. Share the sheet's progress with backdrop movement; do not run unrelated clocks.

Recognize a downward drag after intent is clear. Outside the handle/header, start only when every nested scroller was at its top at gesture start. Reaching the top during a scroll must not unexpectedly convert it to dismissal. Exclude controls, links, form fields, option labels, and toasts. Reserve browser edge gestures.

Track the finger directly without a CSS transition during dragging. On release, use displacement, recent velocity, and reversal direction; an old fast movement followed by a pause is not a flick. On a short drag, cancellation, extra pointer, or loss of focus, settle safely. Suppress only the accidental click from a recognized drag, not future legitimate activation.

Treat `lostpointercapture` carefully: transfer from a child can bubble through the parent. Cancel a parent's drag for actual loss of its own capture, not any bubbled event. Test beginning on text and images with trusted input.

Close once. Explicit visual close may await exit; browser Back and completed destructive operations must retain their immediate route/state semantics. Invalidate delayed callbacks after teardown. If a close-and-navigate operation replaces a normal close callback, run only the selected completion.

Keep notices inside a modal's DOM ownership. A supported manual popover/top-layer surface can escape clipping by transformed ancestors while retaining modal ownership; provide an appropriate fallback. A larger z-index alone cannot escape the browser top layer. Do not autofocus a toast or expose visual duplicates to assistive technology.

## Photo crop example

Keep custom pan/pinch confined to the crop surface. Anchor pinch at the fingers' midpoint; preserve position when changing pointer count; recompute bounds after zooming out. Provide keyboard controls and a reset. Keep file decoding/export errors recoverable and revoke temporary object URLs. Saving commits the crop; closing discards the draft. Buma uses a 2048px working image, cover as minimum zoom, 6x maximum, and a 512px square JPEG export; these are product settings, not universal requirements.
