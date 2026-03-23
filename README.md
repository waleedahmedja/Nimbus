<div align="center">

# N I M B U S

**A weather app that shows you the sky, not a dashboard.**
Built with Jetpack Compose. Designed to be accurate, calm, and worth a second glance.

<br/>

[![Kotlin](https://img.shields.io/badge/Kotlin-2.0+-7F52FF?style=flat-square&logo=kotlin&logoColor=white)](https://kotlinlang.org)
[![Compose](https://img.shields.io/badge/Jetpack_Compose-latest-4285F4?style=flat-square&logo=jetpackcompose&logoColor=white)](https://developer.android.com/jetpack/compose)
![API](https://img.shields.io/badge/Min_SDK-26-green?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)
![Status](https://img.shields.io/badge/Status-Active_Development-FFD60A?style=flat-square)

<br/>

</div>

---

## The Idea

Most weather apps show you a number and a sun icon.

The good ones added personality. The great ones added precision. Carrot made you laugh. Dark Sky made you trust it.

Nimbus does neither — and both.

It shows you the sky. A generative canvas that shifts with time of day, weather condition, and your system theme. A temperature number so large it feels architectural. A single AI-generated sentence that sounds like someone who checked the window this morning.

No comedy. No constant noise. Just weather, told with craft.

> *Does this app make you stop and look at it for a second?*

That's the only metric that matters.

---

## What It Does

| Feature | Description |
|---|---|
| **Sky Canvas** | Generative Compose Canvas — shifts with time of day, condition, and system theme |
| **Daily Sentence** | One AI-generated line per day. Human-sounding. Never robotic. Cached so it doesn't burn API budget. |
| **Rain Timeline** | Minute-by-minute precipitation bar — the Dark Sky feature, rebuilt natively in Compose |
| **7-Day Spline** | Temperature across the week rendered as a landscape curve, not a table |
| **Sunrise / Sunset Arc** | Drawn on Canvas, tracking actual solar position in real time |
| **Air Quality** | One word. Good. Fair. Poor. |
| **System-Adaptive Theme** | Full light and dark mode. Follows Android system preference. Both feel native. |
| **Squircle Shape System** | Every interactive surface uses a consistent squircle corner token |

---

## What It Doesn't Do

- Push notifications about things you already knew
- Gamification, streaks, or engagement mechanics
- Rotating personality that gets old in two weeks
- Settings pages longer than the forecast
- Radar maps nobody actually uses

---

## Architecture

Nimbus follows clean MVVM with unidirectional data flow throughout.

```
app/
├── data/
│   ├── api/              # Open-Meteo + Claude API via Retrofit
│   ├── repository/       # WeatherRepository — single source of truth
│   └── datastore/        # UserPreferences — units only
├── domain/
│   ├── model/            # Weather, HourlyForecast, DailyForecast, SkyCondition
│   └── usecase/          # One use case per data concern
├── presentation/
│   ├── home/             # HomeScreen, HomeViewModel, HomeUiState
│   ├── hourly/           # HourlyScreen, HourlyViewModel
│   ├── weekly/           # WeeklyScreen, WeeklyViewModel
│   └── settings/         # SettingsScreen — units toggle only
├── ui/
│   ├── canvas/           # SkyCanvas, SkyPalette, CloudLayer, SunMoonArc
│   ├── components/       # TemperatureDisplay, RainTimeline, WeekSpline, WaleedSignature
│   └── theme/            # Theme, Color, Type, Shape
└── util/                 # SolarCalculator, LunarCalculator, WeatherMapper
```

Full breakdown in [ARCHITECTURE.md](ARCHITECTURE.md).

**Key principles:**
- UI reads from `StateFlow` — never mutates ViewModel state directly
- All persistence is async via DataStore — no blocking main thread calls
- Every repository function returns `Result<T>` — errors surface cleanly, never crash
- Sky canvas recomposes only on input change — not every frame

---

## Design

The UI is built around one principle: **the sky is the interface**.

- Full-bleed generative canvas behind the entire home screen — not a background, the UI
- OLED-first dark mode — true `#000000` backgrounds, every pixel off is battery saved
- Single accent: Apple Yellow `#FFD60A` for temperature highs and key highlights
- Light mode lifts the same palette — same design language, different atmosphere
- Typography: DM Serif Display for the temperature, DM Sans everywhere else
- Squircle corner token (`32.dp`) on every button — consistent, slightly unexpected
- `waleedahmedja` signature at the bottom of scroll — monospaced, 25% opacity, for people who look

---

## Built With

- **Kotlin 2.0+** + Coroutines + Flow
- **Jetpack Compose** + Material3
- **Open-Meteo** — free, no API key, accurate
- **Claude API** (Haiku) — one call per day for the daily sentence
- **Fused Location Provider** — single auto-detected location
- **Nominatim** — free OpenStreetMap geocoding
- **Retrofit + OkHttp** — networking
- **kotlinx.serialization** — JSON parsing
- **Hilt** — dependency injection
- **DataStore** — async preferences
- **MVVM + StateFlow** — architecture

---

## Getting Started

**Prerequisites:**
- Android Studio Hedgehog or later
- Android SDK 26+
- A Claude API key (for the daily sentence)

**Setup:**

```bash
git clone https://github.com/waleedahmedja/nimbus.git
cd nimbus
```

Add your Claude API key to `local.properties`:

```properties
CLAUDE_API_KEY=your_key_here
```

Open in Android Studio and run on any device or emulator with API 26+.

---

## Documentation

| File | What's inside |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Every structural decision, explained |
| [DESIGN.md](DESIGN.md) | Sky palette, typography, spacing, shape tokens |
| [SKYCANVAS.md](SKYCANVAS.md) | How the canvas renderer works, the solar position maths |
| [ROADMAP.md](ROADMAP.md) | V1 scope, V2 ideas, what's deliberately not shipping |
| [PRIVACY.md](PRIVACY.md) | What Nimbus does and doesn't do with your data |
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to contribute without breaking the philosophy |
| [CHANGELOG.md](CHANGELOG.md) | Version history |

---

— **waleedahmedja**
