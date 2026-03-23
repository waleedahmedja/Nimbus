# Contributing to Nimbus

Nimbus is a weather app with a clear design philosophy.

Before contributing, understand what this project is and what it refuses to be.

---

## Philosophy

Nimbus must remain:

- **Calm** — no feature raises the noise floor
- **Intentional** — nothing exists by accident
- **Restrained** — if it can be removed without breaking understanding, remove it
- **Performant** — smooth on mid-range devices, OLED-considerate

If your contribution adds complexity, it must add clarity by a greater margin.

**Non-negotiable:**
- No gamification, streaks, or engagement mechanics
- No additional notification types beyond rain alerts
- No analytics or usage tracking
- No cloud sync
- No social features
- No second accent colour

---

## Architecture Rules

Follow the established patterns. Do not introduce new ones without discussion.

- **MVVM strictly** — UI reads `StateFlow`, never mutates ViewModel state directly
- **DataStore only** — no SharedPreferences, no Room unless session history is added with clear justification
- **Coroutines + Flow** — no RxJava, no LiveData
- **`Result<T>` for all repository functions** — errors surface cleanly
- **Kotlin only** — no Java files
- **Jetpack Compose only** — no XML layouts

New dependencies require justification in the PR description. If the same result can be achieved with what's already in the project, that approach wins.

---

## UI / UX Guidelines

The visual language is strict. Read [DESIGN.md](DESIGN.md) before touching any screen.

- **OLED-first dark mode** — backgrounds are `#000000`, not `#1A1A1A` or `#121212`
- **Single accent** — Apple Yellow `#FFD60A` in dark mode, `#D4A017` in light mode. No secondary accent colours.
- **Squircle buttons** — `NimbusShapes.ExtraLarge` (`32.dp`) on every interactive surface that deserves emphasis. No exceptions.
- **Edge to edge always** — no coloured status bar, no coloured navigation bar
- **Spacing** — 8dp grid, no exceptions. Minimum `16.dp` between elements.
- **Typography** — DM Serif Display for the temperature, DM Sans everywhere else, JetBrains Mono for the signature
- **Animations** — functional only. If removing the animation makes the UI clearer, remove it. The sky canvas is the sole exception.
- **No shadows** — depth is expressed through brightness contrast in dark mode

If a design decision isn't covered here, the guiding question is: *does this belong in a considered, premium weather app, or does it look like it was assembled without thought?*

---

## How to Contribute

1. **Fork** the repository
2. **Create a branch** — `fix/what-it-fixes` or `improve/what-it-improves`
3. **Make your changes** — keep commits focused and clearly described
4. **Test on a real device** — the emulator is not sufficient for canvas performance validation
5. **Open a Pull Request** with:
   - A clear description of what changed and why
   - Screenshots or a screen recording if the change is visual
   - Confirmation that the sky canvas, daily sentence, and data flow are unaffected

---

## Before Submitting

Verify:

- [ ] Tested on a physical device (not emulator only)
- [ ] Dark mode looks correct — true black backgrounds, yellow accent only where specified
- [ ] Light mode looks correct — nothing washed out, nothing harsh
- [ ] Sky canvas renders without jank at 60fps
- [ ] `waleedahmedja` signature is untouched — position, opacity, font, size
- [ ] No new lint warnings introduced
- [ ] No new dependencies added without justification in the PR

---

## Bug Reports

Open an issue with:
- Device model and Android version
- Steps to reproduce
- Expected behaviour vs actual behaviour
- Whether it occurs in light mode, dark mode, or both
- Logcat output if relevant — redact anything personal

---

## Questions

Open a discussion, not an issue. Issues are for confirmed bugs and accepted feature work.

---

Nimbus respects craft.

Contributions should too.

— **waleedahmedja**
