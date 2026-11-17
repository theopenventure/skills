# Sources and scope

This is an independently organized extraction of Buma's interaction decisions and implementation lessons, dated 16 November 2026. It is intended to work without access to Buma or its private design files. Local source paths below are provenance, not required dependencies.

## Source hierarchy

Use explicit recent product decisions, current shared implementation, and dated verification together. Investigate conflicts rather than silently combining incompatible historical recipes. Buma is React/Vite; the surrounding Fidelify monorepo uses different architecture and tokens. Do not transplant legacy Next.js instructions or `FL_*` tokens into Buma.

The reviewed Buma checkout HEAD was `ab535d04532d420fae17e443dfacf74e72f24f8a`; the extraction reads the working tree, which may include local changes beyond that commit. This identifier is provenance, not a claim that every extracted setting is committed there.

| Knowledge | Source paths relative to Buma |
| --- | --- |
| Current decisions and shared architecture | `AGENTS.md`, `CLAUDE.md` |
| Strategic emphasis on continuity | `docs/estrategia.md` |
| Original mobile decisions and later exceptions | `docs/mobile-native.md`, `docs/mobile-native-2026-10-22.md`, `.agents/skills/mobile-native/SOURCE.md` |
| Viewport and geometry | `src/features/signup/useFormViewport.ts`, `FormViewport.module.css` in the same folder |
| Global behavior and sizing | `src/styles/global.css`, `src/styles/tokens.css` |
| Scroll and browser refresh | `src/lib/lockDocumentScroll.ts`, `src/lib/preventPullToRefresh.ts` |
| Press and motion | `src/components/PressFeedback.module.css`, `src/lib/motionTokens.ts` |
| Sheets | `docs/drawer-motion.md` |
| Navigation and interruption | `docs/navigation-motion-2026-10-26.md`, current cadence decisions in `AGENTS.md` |
| Native editing and synthetic-test trap | `docs/text-fields-motion-2026-10-30.md`, `docs/native-text-entry-fix-2026-10-30.md` |
| Physical-device evidence and measurement limits | `docs/device-performance.md` |

## External reference

Repository organization and mobile foundations were informed by [Emil Kowalski's skills](https://github.com/emilkowalski/skills). Buma previously installed his `mobile-native` skill at commit `2d228630ba1446365f614a108cb8252f1feda202`; its MIT attribution is retained in this repository's third-party notice. The current upstream README and mobile-native overview were reviewed on 16 November 2026.

This skill adds Buma's form-shell, shared lifecycle, navigation, gesture, and evidence contracts. It does not bundle Emil's skill, Buma's assets, fonts, private customer data, product secrets, or business logic. It is not an endorsement or affiliation.

The implementation checklist was added on 17 November 2026, after porting the profile to a second product (Next.js App Router) showed that a symptom-driven pass left the global shell untouched. Its code is adapted from the same Buma working tree (`styles/global.css`, `lib/preventPullToRefresh.ts`, `lib/preventPinchZoom.ts`, `lib/lockDocumentScroll.ts`, `components/PortraitOnly.tsx`, `lib/drawerMotion.ts`).
