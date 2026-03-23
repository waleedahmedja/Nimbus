<div align="center">

  # N I M B U S

Weather, told beautifully.

Nimbus is an Android weather app built with Kotlin and Jetpack Compose. It doesn't try to be funny. It doesn't gamify the forecast. It just shows you the sky — accurately, calmly, and with unreasonable attention to detail.

<br>

![Platform](https://img.shields.io/badge/platform-Android-3DDC84?style=flat-square&logo=android&logoColor=white)
![Kotlin](https://img.shields.io/badge/kotlin-2.0+-7F52FF?style=flat-square&logo=kotlin&logoColor=white)
![Compose](https://img.shields.io/badge/jetpack_compose-latest-4285F4?style=flat-square&logo=jetpackcompose&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-lightgrey?style=flat-square)
![Status](https://img.shields.io/badge/status-active_development-FFCC00?style=flat-square)

---

## What it does

- **Live sky canvas** — a generative Canvas renderer that shifts with time of day, weather condition, and system theme. Not a stock image. Not a Lottie file. Real Compose drawing.
- **One daily sentence** — AI-generated, human-sounding, never robotic. The kind of thing a friend says after checking the window.
- **Rain timeline** — minute-by-minute precipitation bar, rebuilt natively in Compose.
- **7-day spline** — temperature across the week rendered as a landscape curve, not a table.
- **Sunrise / sunset arc** — drawn on Canvas, tracking actual solar position.
- **Adaptive theming** — full light and dark mode that follows system preference. Both feel native. Neither feels like an afterthought.
- **Air quality** — one word. Good. Fair. Poor. That's all anyone needs.

---

## What it doesn't do

- Notifications that scream at you
- Gamification, streaks, or badges
- Radar maps nobody actually uses
- Settings pages that take longer to read than the forecast
- Personality that wears out after two weeks

---

## Screenshots

> Coming once V1 UI is locked.

---

## Tech stack

| Layer | Technology |
|---|---|
| Language | Kotlin 2.0+ |
| UI | Jetpack Compose |
| Architecture | MVVM + StateFlow |
| Weather data | Open-Meteo (free, no API key) |
| AI sentence | Claude API (claude-haiku) |
| Location | Fused Location Provider |
| Geocoding | Nominatim / OpenStreetMap |
| HTTP | Retrofit + OkHttp |
| Serialization | kotlinx.serialization |
| Dependency injection | Hilt |
| Preferences | DataStore |

---

## Getting started

### Prerequisites

- Android Studio Hedgehog or later
- Android SDK 26+
- A Claude API key (for the daily sentence feature)

### Setup

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

## Project structure

```
app/src/main/java/com/waleedahmedja/nimbus/
├── data/               # API, DTOs, repository, DataStore
├── domain/             # Clean models, use cases
├── presentation/       # ViewModels, UI state
├── ui/                 # Screens, components, canvas renderers, theme
└── util/               # Solar/lunar calculators, mappers
```

Full breakdown in [ARCHITECTURE.md](ARCHITECTURE.md).

---

## Documentation

| File | What's inside |
|---|---|
| [ARCHITECTURE.md](ARCHITECTURE.md) | Every structural decision, explained |
| [DESIGN.md](DESIGN.md) | Sky palette, typography, spacing, shape tokens |
| [SKYCANVAS.md](SKYCANVAS.md) | How the canvas renderer works, the solar position maths |
| [ROADMAP.md](ROADMAP.md) | V1 scope, V2 ideas, what's deliberately out |
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to contribute cleanly |
| [CHANGELOG.md](CHANGELOG.md) | Version history |

---

## Roadmap

V1 is scoped tight and intentional. See [ROADMAP.md](ROADMAP.md) for what's locked, what's coming, and what's deliberately never shipping.

---

## License

MIT — see [LICENSE](LICENSE).

---


— **waleedahmedja**
