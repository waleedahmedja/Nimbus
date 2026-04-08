<div align="center">

# N I M B U S

**Not your average weather app.**

Built with Kotlin and Jetpack Compose. Designed with intent.

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

Nimbus fills a gap that still exists on Android — a weather app built with the craft and restraint you'd expect from the best iOS apps. Nothing to prove. No rotating personality that wears out after two weeks. No gamification, no streaks, no nudges.

No comedy. No constant noise. Just weather, told with craft.


---

## What It Does

| Feature | Description |
|---|---|
| **Sky canvas** | Generative Compose `Canvas` — time-aware, condition-reactive, system-theme-adaptive |
| **Daily sentence** | Searches a library of lines, selected by condition + time of day. No AI. No network. Instant. |
| **Real air quality** | European AQI from Open-Meteo — five levels, hidden when data is unavailable |
| **Rain timeline** | Minute-by-minute precipitation bar for the next 24 hours |
| **7-day spline** | Temperature across the week as a landscape curve, not a table |
| **Sunrise / sunset arc** | Drawn on canvas at the correct solar position |
| **Multiple locations** | Up to 3 saved locations. Swipe to switch. Sky transitions between them. |
| **City search** | Forward geocoding via Nominatim — add any city by name |
| **Pull to refresh** | Standard gesture, clean indicator |
| **Hourly detail** | Full 48-hour breakdown with condition, temperature, and precipitation per hour |
| **Weekly detail** | Full 7-day breakdown with high, low, rain, UV per day |
| **System-adaptive theme** | Full light and dark mode. Follows Android system preference. Both feel native. |
| **Squircle design system** | Consistent 32dp corner radius on every interactive surface |
| **Onboarding** | Shown once on first install. Never again. |

---

## What It Doesn't Do

- Notifications that scream at you
- Gamification, streaks, or engagement mechanics
- Radar maps nobody actually uses
- Settings pages longer than the forecast
- Personality that wears out after two weeks
- Ads of any kind

---

## Architecture

Nimbus follows clean MVVM with unidirectional data flow throughout.

```
├── data/
│   ├── api/          # Retrofit interfaces — Open-Meteo, Nominatim
│   ├── datastore/    # UserPreferences — units, locations, city name cache
│   └── repository/   # Single network call returns full forecast bundle
├── domain/
│   ├── model/        # Weather, HourlyForecast, DailyForecast, SavedLocation, SkyCondition
│   └── usecase/      # GetForecastBundleUseCase, GetDailySentenceUseCase, SearchCityUseCase
├── presentation/
│   ├── home/         # HomeScreen, HomeViewModel, HomeUiState
│   ├── hourly/       # HourlyScreen
│   ├── weekly/       # WeeklyScreen
│   ├── onboarding/   # OnboardingScreen — shown once
│   ├── location/     # LocationPermissionScreen
│   ├── settings/     # SettingsScreen
│   └── legal/        # LegalScreen — privacy policy + terms
├── ui/
│   ├── canvas/       # SkyCanvas, SkyPalette, CloudLayer, SunMoonArc
│   ├── components/   # 10 shared composables
│   ├── navigation/   # NimbusNavHost — 8 routes
│   └── theme/        # Theme, Color, Type, Shape, Spacing
├── di/               # Hilt modules
└── util/             # WeatherMapper, SolarCalculator, LunarCalculator, WeatherSentences
```

**Key principles:**
- UI reads from `StateFlow` — never mutates ViewModel state directly
- One Open-Meteo call returns weather + hourly + daily + AQI together
- Circuit breaker prevents hammering a failing API
- Three-tier cache: fresh (< 30min) / stale / very stale
- Sentences are local — no network, no loading state, always instant

---

## Built with

| Layer | Choice |
|---|---|
| Language | Kotlin 2.0+ |
| UI | Jetpack Compose + Material3 |
| Architecture | MVVM + StateFlow + Hilt |
| Weather data | Open-Meteo (free, no API key) |
| Geocoding | Nominatim / OpenStreetMap (free, no API key) |
| HTTP | Retrofit + OkHttp |
| Serialisation | kotlinx.serialization |
| Persistence | DataStore Preferences |
| Location | Fused Location Provider |
| Navigation | Compose Navigation |

No analytics. No crash reporting. No third-party tracking of any kind.

---

## Getting started

**Prerequisites:**
- Android Studio Hedgehog or later
- Android SDK 26+
- A device or emulator running API 26+

**Clone and run:**

```bash
git clone https://github.com/waleedahmedja/nimbus.git
cd nimbus
```

Open in Android Studio. Sync Gradle. Run on a physical device for the canvas to perform correctly — the emulator is sufficient for functionality but not for 60fps canvas validation.

No API keys required. Open-Meteo and Nominatim are both free and keyless.


---

— **waleedahmedja**
