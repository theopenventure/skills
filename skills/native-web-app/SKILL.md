---
name: native-web-app
description: Build and refine mobile web apps that feel native through stable keyboards and viewports, touch feedback, gesture ownership, bottom sheets, scroll restoration, interruptible motion, and state continuity. Use when a mobile website feels unlike an app, an iPhone keyboard shifts the layout, a drawer fights scrolling, navigation jumps, or a user asks for Buma-style native behavior. For browser-based apps, including React and PWAs; not a React Native or SwiftUI implementation guide.
---

# Native Web App

Make the interface respond as one continuous system. Preserve the product's visual identity and native editing, scrolling, and navigation. A native feeling comes from predictable behavior, not adding an animation to every element.

## Start from the actual product

1. Read repository instructions, installed framework versions, current components, tokens, and interaction contracts. Follow the newest explicit decision for a subject. Historical audits can describe problems already fixed.
2. Identify the affected journey and its owners: route/history, viewport, scroll lock, dialog, press feedback, gestures, and motion. Reuse these owners across features; do not build a second shell for another audience.
3. Reproduce the symptom and record the browser, viewport, input method, and available device evidence. Separate an observed defect from a proposed enhancement.
4. For an audit, report prioritized findings with file locations, causes, and verification steps. For an implementation request, make the smallest complete correction within the authorized scope. Do not stop at advice or introduce unrelated features.
5. Read only the references needed below. Use the Buma profile only when explicitly requested or preserving an existing Buma decision. Do not impose its restrictions on other products.

## Load the relevant reference

| Work | Read |
| --- | --- |
| Viewport, keyboard, safe areas, form layout | [Viewport and input](references/viewport-and-input.md) |
| Press, scrolling, sheets, carousels, crop gestures | [Touch and overlays](references/touch-and-overlays.md) |
| Navigation, animation, snapshots, state continuity | [Motion and navigation](references/motion-and-navigation.md) |
| Exact Buma settings and deliberate exceptions | [Buma profile](references/buma-profile.md) |
| Verification and physical-device evidence | [Validation](references/validation.md) |
| Origins and resolving historical conflicts | [Sources](references/sources.md) |

## Non-negotiable interaction contracts

- Keep a real input authoritative for value, caret, selection, autofill, composition, and accessibility. Visual copies are temporary, inert, and hidden from assistive technology.
- Give immediate press feedback, but activate actions with semantic click/keyboard behavior. Never move saving, deleting, or navigation to pointer-down.
- Assign each gesture one owner. Keep ordinary scrolling native; protect browser edge navigation and editable controls.
- Preserve route identity, draft values, carousel selection, scroll, and return focus where the journey requires continuity.
- Commit business state independently of presentation. An interrupted animation cannot lose or duplicate a save, grant, redemption, or navigation.
- Use one motion token source and one end-to-end navigation budget. A stagger consumes that budget; it does not extend it.
- Handle reduced motion initially and when the preference changes. End presentation on interruption, hidden page, route teardown, and incompatible viewport changes.
- Preserve zoom, visible keyboard focus, selectable content, and orientation support by default. Restrictions are product exceptions, not evidence of native quality.
- Clean up RAFs, timers, observers, listeners, snapshots, pointer capture, and scroll locks. Make completion idempotent.
- Do not add installation prompts, service workers, vibration, offline writes, or a native wrapper simply to make the app feel native. These are separate capabilities with their own requirements.

## Finish with evidence

Run the project's relevant checks and the behavioral cases in [Validation](references/validation.md). Report what changed, what was tested, and what remains unverified on a physical phone. Never call desktop emulation a real-device test. Never claim this skill converts a web app into native software.
