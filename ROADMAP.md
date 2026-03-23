# Roadmap

Nimbus ships when it's right. Not when it's complete. These are two different things.

---

## Short Version

V1 is a single-location weather app with a generative sky and a daily sentence. Nothing more. The constraint is the point.

---

## V1 — Locked Scope

| Feature | Status |
|---|---|
| Sky canvas — generative, time-aware, condition-reactive, system-adaptive | Planned |
| Current weather — temperature, feels-like (conditional), condition, air quality | Planned |
| Sunrise / sunset arc with real solar position | Planned |
| Daily AI sentence — one line, cached per day, human-sounding | Planned |
| Hourly forecast — rain timeline bar, temperature by hour (48h) | Planned |
| 7-day spline — temperature landscape curve, tap to expand | Planned |
| Full light and dark mode — follows system, no toggle | Planned |
| Squircle shape system — consistent across every interactive surface | Planned |
| Units setting — °C/°F, km/h/mph | Planned |
| `waleedahmedja` signature — scroll bottom, monospaced, 25% opacity | Planned |
| Auto location — Fused Location Provider, single location | Planned |

**Not in V1 — and won't be added mid-development:**
- Multiple saved locations
- Push notifications of any kind
- Radar map
- Home screen widget
- Severe weather alerts
- Onboarding flow
- Easter eggs

---

## V2 — Under Consideration

These are real features that could ship in V2. None are guaranteed. Each is evaluated against the core question: does it make the app calmer and more useful, or does it add noise?

### High likelihood

**Rain alert — one notification.**
If precipitation is expected within 60 minutes and the user hasn't opened the app today — one notification. Not a daily digest. Not a nudge. One useful signal, once, when it matters. User can disable it entirely. No sub-toggles.

**Saved locations — up to 3.**
Swipe left/right on the home screen. No list view, no search history. Three slots the user controls.

**Home screen widget.**
Single glance. Current temp, condition icon, one-word air quality. No more. Same restraint as the app.

**Easter egg.**
Long press the `waleedahmedja` signature for 2 seconds. Something happens. Beautiful and unexpected. Completely undocumented. V2 only — it should feel discovered, not advertised.

### Possible

**Week narrative sentence.**
Sunday mornings, a single AI sentence describing the coming week's weather shape. Delivered as a notification or surfaced on the weekly view. Not both.

**Commute window.**
User sets their typical commute time. On weekdays, if weather during that window is significantly worse than the rest of the day, the daily sentence adapts. No separate feature surface — it integrates into the existing sentence.

**Wear OS complication.**
Temperature and condition, updated hourly. Taps through to the phone app.

### Low likelihood

**Radar map.**
Opens a complexity door that's hard to close. Most users never use it meaningfully. If it ever ships, it's a separate full-screen view — never embedded in the home scroll.

---

## Development Phases

**Phase 1 — Foundation**
Architecture, data layer, Open-Meteo integration, domain models, Hilt setup, theme system.

**Phase 2 — Sky Canvas**
`SkyCanvas.kt`, `SkyPalette.kt`, `CloudLayer.kt`, `SunMoonArc.kt`. The hardest phase technically. The most important visually. Nothing ships until the canvas is right.

**Phase 3 — Home Screen**
`HomeScreen.kt`, `HomeViewModel.kt`, `TemperatureDisplay.kt`, `DailySentenceCard.kt`, `WaleedSignature.kt`. Claude API integration for the daily sentence.

**Phase 4 — Hourly and Weekly**
`HourlyScreen.kt`, `RainTimeline.kt`, `WeeklyScreen.kt`, `WeekSpline.kt`. Data-dense views built to be readable without being overwhelming.

**Phase 5 — Polish**
Animation timing. Edge cases. Error states. Skeleton loading. Low-end device testing. Accessibility pass. This phase takes longer than expected. That's where the craft lives.

**Phase 6 — Release**
Play Store listing. Screenshots. Store description. GitHub repo final cleanup. `CHANGELOG.md` V1.0 entry.

---

## What Will Never Ship

Some things aren't in the roadmap because they're being saved for later. Some aren't because they're wrong.

These will never be in Nimbus:

- Gamification. No streaks, badges, or check-in rewards.
- Multiple notification types. One type maximum — rain alerts — fully dismissable.
- A personality mode or tone setting. Nimbus has one voice. It doesn't change.
- Ads or sponsored content.
- Dark patterns — no permission requests beyond what's necessary, no upsell flows, no rating prompts after two sessions.
- An AI assistant mode. Claude exists for the daily sentence. That's where it ends.
- Social features.

---

— **waleedahmedja**
