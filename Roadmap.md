# Roadmap

Nimbus ships when it's right, not when it's complete. These are two different things.

---

## V1 — locked scope

V1 is a single-location weather app with a generative sky and an AI-generated daily sentence. Nothing more. The constraint is the point.

### Ships in V1

- [x] Sky canvas — generative, time-aware, condition-reactive, system-theme-adaptive
- [x] Current weather — temperature, feels-like (conditional), condition, air quality (one word)
- [x] Sunrise / sunset arc with real solar position
- [x] Daily AI sentence — one line, cached per day, human-sounding
- [x] Hourly forecast — rain timeline bar, temperature by hour (48h)
- [x] 7-day spline — temperature landscape curve, tap to expand
- [x] Full light and dark mode — follows system, no toggle
- [x] Squircle shape system — consistent across every interactive surface
- [x] Units setting — °C/°F, km/h/mph. The only setting in the app.
- [x] `waleedahmedja` signature — bottom of scroll, monospaced, 25% opacity
- [x] Auto location — Fused Location Provider, single location only

### Not in V1 (and won't be added mid-development)

- Multiple saved locations
- Push notifications of any kind
- Radar map
- Home screen widget
- Lock screen complication
- Severe weather alerts beyond the daily sentence
- Onboarding flow
- Easter eggs (V2 only — earned, not rushed)

---

## V2 — ideas under consideration

These are real features that could ship in V2. None are guaranteed. Each will be evaluated against the core philosophy: does it make the app calmer and more useful, or does it add noise?

### High likelihood

**Rain alert — one notification.**
If precipitation is expected within the next 60 minutes and the user hasn't opened the app today, send one notification. Not a daily digest. Not a streak reminder. One useful nudge, once, when it matters. User can disable it entirely in settings — no sub-toggles.

**Saved locations — up to 3.**
Swipe left/right on the home screen to switch between saved cities. No list view, no search history, no "popular cities." Just three slots the user controls.

**Home screen widget.**
Single glance. Current temperature, condition icon, one-word air quality. No more. The widget is Nimbus on the home screen — same restraint, same craft.

**Easter egg.**
Long press the `waleedahmedja` signature for 2 seconds. Something happens. It will be beautiful and unexpected and completely undocumented. V2 only — it should feel discovered, not advertised.

### Possible

**Week narrative sentence.**
Sunday mornings, a second AI sentence that describes the shape of the coming week. "Sunny through Wednesday, then a cold front arrives Thursday." Delivered as a notification or surfaced on the weekly view. Not both.

**Commute window.**
User sets their typical commute time. On weekdays, if weather during that window is significantly worse than the rest of the day, the daily sentence adapts to mention it. No separate feature surface — it integrates into the existing sentence.

**Wear OS complication.**
Temperature and condition, updated hourly. Taps through to the phone app. Small, clean, on brand.

### Low likelihood (tracked for honesty, not aspiration)

**Radar map.**
Opens a complexity door that's hard to close. Most users never use it meaningfully. If it ships, it ships as a separate full-screen view, never embedded in the home scroll.

**Hyperlocal precipitation (minute-by-minute rain alerts).**
Dark Sky made this famous. Tomorrow.io has the API. The engineering cost and API cost are both non-trivial. V3 territory if it ever happens.

---

## What will never ship

Some things aren't in the roadmap because they're being saved. Some are there because they're wrong.

**These will never be in Nimbus:**

- Gamification. No streaks, no badges, no "you've checked the weather 7 days in a row."
- Multiple notification types. One type maximum — rain alerts — and the user can turn it off entirely.
- A personality mode or tone setting. Nimbus has one voice. It's calm. It doesn't change.
- Ads or sponsored content of any kind.
- Dark patterns — no permission requests that aren't strictly necessary, no upsell flows, no rating prompts after two sessions.
- An "AI assistant" mode. The AI exists for the daily sentence. That's where it ends.
- Social features. Weather is not a social activity.

---

## Development phases

### Phase 1 — Foundation (current)
Architecture, data layer, Open-Meteo integration, domain models, Hilt setup, theme system.

### Phase 2 — Sky canvas
`SkyCanvas.kt`, `SkyPalette.kt`, `CloudLayer.kt`, `SunMoonArc.kt`. This is the hardest phase technically and the most important visually. Nothing ships until the canvas is right.

### Phase 3 — Home screen
`HomeScreen.kt`, `HomeViewModel.kt`, `TemperatureDisplay.kt`, `DailySentenceCard.kt`, `WaleedSignature.kt`. The daily sentence and Claude API integration.

### Phase 4 — Hourly + weekly
`HourlyScreen.kt`, `RainTimeline.kt`, `WeeklyScreen.kt`, `WeekSpline.kt`. The data-dense views, built to be readable without being overwhelming.

### Phase 5 — Polish
Animation timing, edge cases, error states, skeleton loading, low-end device testing, accessibility pass. This phase takes longer than expected. That's normal. That's where the craft lives.

### Phase 6 — Release
Play Store listing, screenshots, store description, GitHub repo final cleanup, `CHANGELOG.md` V1.0 entry.

---

— **waleedahmedja**
