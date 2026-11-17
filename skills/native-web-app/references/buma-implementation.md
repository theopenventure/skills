# Buma implementation checklist — explicit opt-in

Use this file when a user asks for "everything Buma does" in another product. It lists the shell-level behavior Buma installs once, with portable code. The product settings and their rationale are in [Buma profile](buma-profile.md); this file is the port.

## Port all of it, then report each item

The observed failure mode was a partial port: drawers, gestures, focus, and history were fixed, while the global shell (selection, zoom, pull-to-refresh, scroll lock, orientation, keyboard in sheets) stayed browser-default. The app still felt like a website.

Before reporting, mark every row below as **applied**, **deliberately different** (with the product reason), or **not applicable** (the product has no such surface). Never stop at a subset silently.

| # | Area | Buma source | Done when |
| --- | --- | --- | --- |
| 1 | Viewport meta | `index.html` | `width=device-width, initial-scale=1, viewport-fit=cover`; no `maximum-scale` or `user-scalable=no` |
| 2 | No pinch or double-tap zoom | `styles/global.css`, `lib/preventPinchZoom.ts` | `touch-action: pan-x pan-y` on every app and dialog element, plus WebKit `gesturestart`/`gesturechange` cancellation on the app root and on each open dialog |
| 3 | No browser pull-to-refresh | `styles/global.css`, `lib/preventPullToRefresh.ts` | `overscroll-behavior-y: contain` on `html, body`, plus the scoped touchmove fallback |
| 4 | No text selection on display text | `styles/global.css` | `user-select: none` and `-webkit-touch-callout: none` on the app tree; editable fields restored to `text`/`default` |
| 5 | Input focus zoom | `styles/global.css` | Inputs, textareas, selects at least 16px |
| 6 | Tap highlight and image drag | `styles/global.css` | `-webkit-tap-highlight-color: transparent`; `img { -webkit-user-drag: none }` |
| 7 | One shared scroll lock | `lib/lockDocumentScroll.ts` | Reference-counted; fixed body at the saved position; restored after the last release |
| 8 | Portrait-only notice | `components/PortraitOnly.tsx` | Coarse-pointer landscape shows a notice; routes stay mounted, hidden and `inert`; shares the scroll lock; focus returns |
| 9 | Drawer gesture numbers | `lib/drawerMotion.ts` | 5px intent, 20px protected side edges, dismiss at `min(150px, 24% height)` or 28px + 0.6px/ms, velocity from the last 100ms |
| 10 | Keyboard inside sheets | `features/signup/useFormViewport.ts` pattern | Sheets with fields follow `visualViewport`; the focused field stays visible |
| 11 | Return after background | `useResendCooldown.ts`, `useWalletMessages.ts` | Time- or day-dependent screens refresh on `visibilitychange`/`pageshow` |
| 12 | Selection memory by identity | Wallet/carousel restoration | Carousels and lists restore a stable id, not an index |
| 13 | Motion budget | `lib/motionTokens.ts` | 360ms navigation including delays; easing `cubic-bezier(0.23, 1, 0.32, 1)`; one token source for CSS and JS |
| 14 | Focus modality | `lib/focusVisibility.ts` | Pointer vs Tab tracked for focus restoration. Buma also hides outlines everywhere: a product exception, see the profile |
| 15 | Theme lock | `color-scheme: only light` | Only when the product has no dark mode |

Rows 1–13 are the behavior people notice. Rows 14–15 are product exceptions that need the target product's own decision.

## Global CSS

Frameworks without a `#root` (Next.js App Router) use `body` as the app tree. Keep the whole selector inside `:where()` so component classes with their own `touch-action` (a drawer handle with `none`, a horizontal swiper with `pan-y`) still win.

```css
:where(html, body, body *, dialog, dialog *) {
  touch-action: pan-x pan-y;
  -webkit-tap-highlight-color: transparent;
}
dialog::backdrop {
  touch-action: pan-x pan-y;
}

html,
body {
  overscroll-behavior-y: contain;
}

body,
body :where(*) {
  -webkit-user-select: none;
  user-select: none;
  -webkit-touch-callout: none;
}
body :is(
    input:not([type="radio"], [type="checkbox"], [type="file"], [type="button"], [type="submit"], [type="reset"], [type="range"], [type="color"]),
    textarea,
    [contenteditable]:not([contenteditable="false"])
  ) {
  -webkit-user-select: text;
  user-select: text;
  -webkit-touch-callout: default;
}

/* If a reset sets `font: inherit` on fields, a :where() baseline loses; clamp instead. */
input,
textarea,
select {
  font-size: max(16px, 1em);
}

img {
  -webkit-user-drag: none;
}
```

Remove any older CSS-only lock such as `:root:has(dialog[open]) { overflow: hidden }` once the shared lock exists; two owners drift.

## Shared scroll lock

```ts
let count = 0
let saved: { x: number; y: number; html: string; body: Record<string, string> } | null = null

export function lockDocumentScroll() {
  if (count === 0) {
    const b = document.body.style
    saved = {
      x: scrollX,
      y: scrollY,
      html: document.documentElement.style.overflow,
      body: { position: b.position, top: b.top, left: b.left, width: b.width },
    }
    document.documentElement.style.overflow = 'hidden'
    Object.assign(b, { position: 'fixed', top: `${-saved.y}px`, left: `${-saved.x}px`, width: '100%' })
  }
  count += 1
  let released = false
  return () => {
    if (released) return
    released = true
    count -= 1
    if (count !== 0 || !saved) return
    const s = saved
    saved = null
    document.documentElement.style.overflow = s.html
    Object.assign(document.body.style, s.body)
    scrollTo({ left: s.x, top: s.y, behavior: 'instant' })
  }
}
```

Buma also saves and restores priorities; keep that when other code writes `!important` inline styles.

## Pull-to-refresh fallback (iPhone)

CSS containment alone failed on the reference iPhone. Install on the app root once:

```ts
export function preventPullToRefresh(surface: HTMLElement) {
  let g: { id: number; x0: number; y0: number; prevY: number; scrollers: Element[] } | null = null
  const reset = () => { g = null }
  function start(e: TouchEvent) {
    reset()
    if (e.touches.length !== 1 || !(e.target instanceof Element)) return
    if (e.target.closest('input, textarea, select, [contenteditable]:not([contenteditable="false"])')) return
    const t = e.touches[0]
    const scrollers: Element[] = []
    for (let el: Element | null = e.target; el; el = el.parentElement) {
      if (el.scrollHeight > el.clientHeight && /^(auto|scroll|overlay)$/.test(getComputedStyle(el).overflowY)) scrollers.push(el)
      if (el === surface) break
    }
    if (document.scrollingElement && !scrollers.includes(document.scrollingElement)) scrollers.push(document.scrollingElement)
    g = { id: t.identifier, x0: t.clientX, y0: t.clientY, prevY: t.clientY, scrollers }
  }
  function move(e: TouchEvent) {
    if (e.touches.length !== 1) return reset()
    const t = e.touches[0]
    if (!g || t.identifier !== g.id) return
    const dx = t.clientX - g.x0, dy = t.clientY - g.y0, down = t.clientY > g.prevY
    g.prevY = t.clientY
    if (!e.cancelable || e.defaultPrevented || !down || dy <= Math.abs(dx)) return
    if (g.scrollers.some((el) => el.scrollTop > 0)) return
    e.preventDefault()
  }
  surface.addEventListener('touchstart', start, { passive: true })
  surface.addEventListener('touchmove', move, { passive: false })
  surface.addEventListener('touchend', reset, { passive: true })
  surface.addEventListener('touchcancel', reset, { passive: true })
  return () => {
    reset()
    surface.removeEventListener('touchstart', start)
    surface.removeEventListener('touchmove', move)
    surface.removeEventListener('touchend', reset)
    surface.removeEventListener('touchcancel', reset)
  }
}
```

## Pinch guard (WebKit)

```ts
export function preventPinchZoom(surface: HTMLElement) {
  const stop = (e: Event) => { if (e.cancelable && !e.defaultPrevented) e.preventDefault() }
  const o = { capture: true, passive: false } as const
  surface.addEventListener('gesturestart', stop, o)
  surface.addEventListener('gesturechange', stop, o)
  return () => {
    surface.removeEventListener('gesturestart', stop, o)
    surface.removeEventListener('gesturechange', stop, o)
  }
}
```

Install it on the app root and again on each open `dialog`: the top layer is not inside the root.

## Portrait notice

- Landscape only when `(any-pointer: coarse)` matches. Read `screen.orientation.type`, falling back to `window.orientation`. Never infer orientation from viewport height: the keyboard changes it.
- Subscribe to `screen.orientation` change, the pointer media query, `orientationchange`, `pageshow`, and `visibilitychange` (for example through `useSyncExternalStore`).
- Keep the routes mounted under the notice with `visibility: hidden` and `inert`. Take the shared scroll lock, focus the notice, and return focus to the last focused control afterwards.
- The notice copy follows the product's copy rules. If copy is generated, add keys instead of hardcoding text.
- In Next.js App Router, mount the provider as a client component inside `<body>` in the root layout.

## Drawer numbers and keyboard

```ts
export function shouldDismissDrawer(distance: number, velocity: number, height: number) {
  const threshold = Math.min(150, height * 0.24)
  return (distance >= threshold && velocity > -0.3) || (distance >= 28 && velocity >= 0.6)
}
```

- Start a drag only after 5px downward intent; reject upward or mostly sideways movement.
- Ignore pointers that start within 20px of either side edge: browser Back owns them.
- Measure velocity from samples in the last 100ms, so an old flick followed by a pause returns.
- Wrap `setPointerCapture` in `try`: the pointer may already be gone.
- Keyboard: while a sheet with fields is open, read `visualViewport` (`innerHeight - height - offsetTop`), coalesced with RAF. Lift the sheet by the covered height, cap its max height to the visible height, and keep the focused field visible with `scrollIntoView({ block: 'nearest' })`. Remove the values on close.

## Return after background

A screen whose content depends on the date or on server time compares a stored day on `visibilitychange` (visible), `pageshow`, and `focus`, and refreshes only when it changed. Do not refetch on every focus.

## Verify

- Computed styles: `user-select: none` on display text, `text` in a textarea; `touch-action: pan-x pan-y` on a card; `overscroll-behavior-y: contain` on `html`.
- Lock: open a sheet and check `body` is `position: fixed`; close it and check the inline styles and scroll position are restored.
- Drawer: a slow short drag returns, a slow long drag closes, and a fast short flick closes.
- Orientation: emulate a touch viewport under 768px wide in landscape; the notice appears; return to portrait and the screen is unchanged.
- Report rows 1–15 individually, and keep keyboard, safe areas, edge swipes, pinch, and pull-to-refresh pending until a physical phone confirms them.
