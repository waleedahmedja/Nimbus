# Contributing to Nimbus

Nimbus is a weather app with a clear design philosophy and a tight scope.

Before contributing, understand what the project is — and what it refuses to be.

---

## Read this first

Nimbus is not a community project in the sense of "the more the merrier." The design language, architecture, and philosophy are specific. Contributions that add noise, complexity, or features outside the roadmap won't be merged — regardless of how well they're written.

If you're unsure whether your contribution fits, open a discussion before writing a single line of code. A five-minute conversation saves both of us time.

---

## Philosophy

Nimbus must remain:

- **Calm** — no feature raises the noise floor
- **Intentional** — nothing exists by accident
- **Restrained** — if it can be removed without breaking understanding, remove it
- **Accurate** — weather data must be correct; UI elements that could mislead must not exist
- **Performant** — smooth on mid-range devices, OLED-considerate

If your contribution adds complexity, it must add clarity by a greater margin. That's the bar.

**Non-negotiable removals — these will never be added:**
- Gamification, streaks, or engagement mechanics
- Push notifications beyond the single rain alert planned for V2
- Analytics or usage tracking of any kind
- Cloud sync
- Social features
- A second accent colour
- Radar maps embedded in the home scroll
- Any feature that could display inaccurate health or safety information (fake AQI, fabricated alerts)

---

## Architecture rules

Follow the established patterns. Do not introduce new ones without prior discussion.

**Core rules:**
- **MVVM strictly** — UI reads `StateFlow`, never mutates ViewModel state directly
- **Single bundle call** — `getForecastBundle()` makes one network request and returns weather + hourly + daily together. Do not split it back into separate calls.
- **`Result<T>` for all repository functions** — errors surface cleanly, never silently
- **DataStore only** — no `SharedPreferences`, no `Room` unless a genuinely new persistence need is introduced with clear justification
- **Coroutines + Flow** — no RxJava, no `LiveData`
- **Kotlin only** — no Java files
- **Jetpack Compose only** — no XML layouts

**New dependencies:**
Every new dependency requires justification in the PR description. If the same result can be achieved with what's already in the project, that approach wins. Nimbus has no unused dependencies and that's not accidental.

---

## UI and design rules

Read [DESIGN.md](DESIGN.md) before touching any screen. The visual language is specific and strict.

**Colours:**
- Dark mode backgrounds: `#000000` — not `#1A1A1A`, not `#121212`, not "close enough"
- Accent: Apple Yellow `#FFD60A` (dark) / `#D4A017` (light). One accent. No exceptions.
- No second accent colour under any circumstances

**Shape:**
- `NimbusShapes.ExtraLarge` (32dp) on every button and interactive surface that deserves emphasis
- Shape tokens are not optional — they are the visual identity

**Typography:**
- DM Serif Display for the temperature number
- DM Sans for everything else
- JetBrains Mono for the `waleedahmedja` signature — and only there

**Canvas:**
- The sky canvas is the signature piece. Treat it with corresponding care.
- Cloud animation performance must not regress on mid-range devices (Snapdragon 680+)
- Crescent moon rendering uses `clipPath` with `ClipOp.Difference` — the overdraw approach is incorrect and must not be reintroduced

**Layout:**
- Edge to edge, always — no coloured status or navigation bars
- 8dp spacing grid — nothing lives between grid points
- No bottom navigation bar

**The `waleedahmedja` signature:**
- Never moved, never restyled, never removed
- Position, opacity, font, and size are fixed

---

## Sentence library

The `WeatherSentences.kt` library has 440 sentences across 6 conditions × 4 time buckets.

If contributing new sentences, follow the tone rules precisely:
- Human and observational — sounds like someone who looked out the window
- Maximum ~15 words
- No exclamation marks
- No emoji
- No weather jargon (no "precipitation", "barometric", "dewpoint", "UV index")
- Works for any city on earth — no geographic assumptions
- Does not start with "Today" or "It is"

Poor: *"Wow, it's really sunny today! Great UV weather!"*
Good: *"The afternoon is holding nothing back."*

---

## How to contribute

1. **Fork** the repository
2. **Open a discussion** if your change is non-trivial — save yourself the effort of a PR that won't land
3. **Create a branch** — `fix/what-it-fixes` or `improve/what-it-improves`
4. **Keep changes focused** — one concern per PR. Don't combine a bug fix with a refactor.
5. **Test on a physical device** — the emulator is not sufficient for canvas performance or haptic validation
6. **Open a Pull Request** including:
   - A clear description of what changed and why
   - Screenshots or screen recording if the change is visual
   - Confirmation that the sky canvas, data flow, and sentence selection are unaffected

---

## Before you submit

Verify every item:

- [ ] Tested on a physical device — not emulator only
- [ ] Dark mode: true black backgrounds, yellow accent only where specified
- [ ] Light mode: nothing washed out, nothing harsh
- [ ] Sky canvas renders at 60fps on a mid-range device
- [ ] Single network call pattern preserved — no split fetches introduced
- [ ] `waleedahmedja` signature: position, opacity, font, size unchanged
- [ ] No new lint warnings
- [ ] No new dependencies without justification in the PR description
- [ ] Air quality badge only shows real European AQI data (1–5) — never a derived approximation

---

## Bug reports

Open a GitHub Issue and include:

- Device model and Android version
- Whether it occurs in dark mode, light mode, or both
- Steps to reproduce — specific enough that someone else can follow them
- Expected behaviour vs actual behaviour
- Logcat output if relevant — redact anything personal before pasting

---

## Feature requests

Open a Discussion, not an Issue. Issues are for confirmed bugs and accepted feature work only.

Read [ROADMAP.md](ROADMAP.md) before opening a feature request. If what you're asking for is in the "will never ship" section, please reconsider.

---

## Questions

Open a Discussion. The project has a clear philosophy and a maintainer who is happy to talk through whether something fits — but not through Issues, which are tracked for actionable work.

---

Nimbus respects craft.
Contributions should too.

---

— **waleedahmedja**
