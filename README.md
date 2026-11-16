# theopenventure / skills

Practical skills for designing and building digital products, drawn from decisions made in real projects.

The first skill comes from building Buma: the small details that make a mobile web app feel coherent, responsive, and close to native.

## Install

Install the collection:

```sh
npx skills add theopenventure/skills
```

Install only the first skill:

```sh
npx skills add theopenventure/skills --skill native-web-app
```

Or copy `skills/native-web-app` into your agent's supported skills directory. The skill is plain Markdown and does not require Buma, a specific framework, or a paid service. The remote installer has not been exercised in this environment.

## Skills

| Skill | What it helps with |
| --- | --- |
| [native-web-app](skills/native-web-app/SKILL.md) | Stable keyboards and viewports, touch feedback, sheets, gesture ownership, scroll restoration, motion, and continuity on mobile web. |

## Try it

> Use native-web-app to audit this mobile app. Find where keyboard, scrolling, gestures, and navigation break the feeling of continuity. Show the causes and a prioritized correction plan.

> Use native-web-app to fix this bottom sheet. It should track the finger, preserve internal scrolling, and restore the page and focus when closed.

> Use native-web-app with the Buma profile to refine this onboarding flow. Keep the real inputs, preserve drafts, and use the shared 360ms navigation budget.

## What makes this collection useful

Each skill captures decisions, implementation traps, and verification criteria—not just visual preferences. General defaults remain accessible; deliberate Buma restrictions are an explicit optional profile. Browser tests and physical-device validation are reported separately.

Instructions are written in English for reuse across projects and agents; prompts and responses can be in Portuguese or another language.

## Adding the next skill

Create a new `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter. Keep the main workflow concise and put detailed guidance in `references/`. Add a row above, validate the metadata and local links, and exercise it on a realistic task before publishing. Keep each skill self-contained; never copy secrets, private data, or unlicensed assets into this collection.

## Acknowledgment

Repository structure inspired by [Emil Kowalski's skills](https://github.com/emilkowalski/skills). The first skill builds on lessons from Buma and acknowledges the mobile-native foundation previously used in that project. See [third-party notices](THIRD_PARTY_NOTICES.md).
