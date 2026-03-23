# Architecture

Nimbus follows clean MVVM with unidirectional data flow. The same architecture used in Score247 and Friction V2. Proven, boring in the best way.

---

## Philosophy

**One source of truth.** `WeatherRepository` owns all data. ViewModels observe it. Screens observe ViewModels. Nothing reaches across layers.

**Dumb screens.** UI files contain zero business logic. They render state and emit events. That's all.

**Minimal state.** Only what the UI actually needs lives in `UiState`. No raw DTOs, no API models leaking into the presentation layer.

**Fail gracefully.** Every network call is wrapped in `Result<T>`. Every `UiState` has an error case. The app never crashes silently.

---

## Layer Map

```
┌──────────────────────────────────────┐
│             UI Layer                 │
│   Screens (Compose) + Components     │
│   SkyCanvas, RainTimeline, Spline    │
└─────────────────┬────────────────────┘
                  │ observes
┌─────────────────▼────────────────────┐
│         Presentation Layer           │
│   ViewModels + UiState (StateFlow)   │
└─────────────────┬────────────────────┘
                  │ calls
┌─────────────────▼────────────────────┐
│           Domain Layer               │
│   Use Cases + Clean Domain Models    │
└─────────────────┬────────────────────┘
                  │ calls
┌─────────────────▼────────────────────┐
│            Data Layer                │
│   Repository → API + DataStore       │
└──────────────────────────────────────┘
```

---

## Full File Structure

```
app/src/main/java/com/waleedahmedja/nimbus/
│
├── data/
│   ├── api/
│   │   ├── OpenMeteoService.kt           # Retrofit interface — weather data
│   │   ├── ClaudeApiService.kt           # Retrofit interface — daily sentence
│   │   └── dto/
│   │       ├── WeatherResponseDto.kt     # Raw Open-Meteo response shape
│   │       ├── HourlyDto.kt              # Hourly data block
│   │       ├── DailyDto.kt               # Daily data block
│   │       └── ClaudeResponseDto.kt      # Claude API response shape
│   │
│   ├── repository/
│   │   └── WeatherRepository.kt          # Coordinates API, cache, and preferences
│   │
│   └── datastore/
│       └── UserPreferences.kt            # Units (°C/°F, km/h/mph), cached daily sentence
│
├── domain/
│   ├── model/
│   │   ├── Weather.kt                    # Current conditions — clean domain model
│   │   ├── HourlyForecast.kt             # Single hour data point
│   │   ├── DailyForecast.kt              # Single day data point
│   │   └── SkyCondition.kt              # Enum: CLEAR, CLOUDY, RAIN, STORM, SNOW, FOG
│   │
│   └── usecase/
│       ├── GetCurrentWeatherUseCase.kt
│       ├── GetHourlyForecastUseCase.kt
│       ├── GetDailyForecastUseCase.kt
│       └── GenerateDailySentenceUseCase.kt
│
├── presentation/
│   ├── home/
│   │   ├── HomeScreen.kt
│   │   ├── HomeViewModel.kt
│   │   └── HomeUiState.kt
│   ├── hourly/
│   │   ├── HourlyScreen.kt
│   │   └── HourlyViewModel.kt
│   ├── weekly/
│   │   ├── WeeklyScreen.kt
│   │   └── WeeklyViewModel.kt
│   └── settings/
│       └── SettingsScreen.kt             # Units toggle — the only setting
│
├── ui/
│   ├── canvas/
│   │   ├── SkyCanvas.kt                  # Root canvas composable — coordinates all layers
│   │   ├── SkyPalette.kt                 # Maps (time, condition, isDark) → gradient colours
│   │   ├── CloudLayer.kt                 # Animated cloud renderer
│   │   └── SunMoonArc.kt                # Solar and lunar arc drawing
│   │
│   ├── components/
│   │   ├── TemperatureDisplay.kt         # The large architectural temperature number
│   │   ├── RainTimeline.kt               # Minute-by-minute precipitation bar
│   │   ├── WeekSpline.kt                 # 7-day temperature spline curve
│   │   ├── DailySentenceCard.kt          # The AI-generated daily line
│   │   ├── AirQualityBadge.kt            # Good / Fair / Poor — nothing else
│   │   ├── SunriseSunsetArc.kt           # Arc with real solar position dot
│   │   └── WaleedSignature.kt            # Craftsman's mark at scroll bottom
│   │
│   └── theme/
│       ├── Theme.kt                      # MaterialTheme wrapper — system-adaptive
│       ├── Color.kt                      # Full palette, light + dark
│       ├── Type.kt                       # Typography scale
│       └── Shape.kt                     # Squircle shape tokens
│
├── di/
│   ├── NetworkModule.kt                  # Hilt: Retrofit, OkHttp providers
│   ├── RepositoryModule.kt               # Hilt: WeatherRepository binding
│   └── DataStoreModule.kt               # Hilt: DataStore singleton
│
├── util/
│   ├── SolarCalculator.kt                # Sunrise, sunset, sun position as 0.0–1.0
│   ├── LunarCalculator.kt                # Moon phase 0–7
│   └── WeatherMapper.kt                  # DTO → domain model
│
└── NimbusApp.kt                          # Application class, Hilt entry point
```

---

## Data Flow — Current Weather

```
HomeScreen
  └── observes HomeViewModel.uiState: StateFlow<HomeUiState>
        └── HomeViewModel init
              └── GetCurrentWeatherUseCase()
                    └── WeatherRepository.getCurrentWeather()
                          ├── OpenMeteoService.getForecast()    [Retrofit]
                          └── UserPreferences.getUnits()        [DataStore]
```

---

## Data Flow — Daily Sentence

Generated once per day. Cached in DataStore. Survives process death.

```
HomeViewModel init
  └── GenerateDailySentenceUseCase()
        └── WeatherRepository.getDailySentence()
              ├── UserPreferences.getCachedSentence()
              │     → return immediately if today's date matches cache date
              └── ClaudeApiService.generate(prompt)
                    → on success: cache result + return
                    → on failure: return static fallback sentence
```

The app never breaks over a sentence. If the Claude API is unavailable, a static fallback is shown. The user never sees an error state for this feature.

---

## UiState Pattern

Every screen follows the same sealed structure:

```kotlin
sealed interface HomeUiState {
    data object Loading : HomeUiState

    data class Success(
        val weather: Weather,
        val hourly: List<HourlyForecast>,
        val daily: List<DailyForecast>,
        val dailySentence: String,
        val sunPosition: Float,        // 0.0 (sunrise) → 1.0 (sunset)
        val moonPhase: Int,            // 0–7
        val skyCondition: SkyCondition,
        val isDark: Boolean            // mirrors system theme for canvas rendering
    ) : HomeUiState

    data class Error(val message: String) : HomeUiState
}
```

---

## Shape System — Squircle Tokens

All interactive surfaces use squircle corners. Consistent. Recognisable. Slightly unexpected.

```kotlin
// Shape.kt
object NimbusShapes {
    val ExtraSmall = RoundedCornerShape(8.dp)     // Badges, chips
    val Small      = RoundedCornerShape(12.dp)    // Timeline segments
    val Medium     = RoundedCornerShape(16.dp)    // Cards, daily sentence
    val Large      = RoundedCornerShape(24.dp)    // Bottom sheets
    val ExtraLarge = RoundedCornerShape(32.dp)    // Buttons — the Nimbus squircle
    val Full       = CircleShape                  // Icon containers only
}
```

`ExtraLarge` is the button token. Every tappable surface that deserves emphasis uses it. Open the app for two minutes and your eye starts to expect it.

---

## API Choices

### Open-Meteo
Free. No API key. No rate limits for reasonable usage. Returns current conditions, hourly (48h), and daily (7d) in a single call. Accuracy on par with commercial providers for most regions.

Endpoint: `https://api.open-meteo.com/v1/forecast`

### Claude API (Haiku)
One call per day maximum. Result cached in DataStore and served for 24 hours. Haiku chosen for speed and cost — the prompt is simple, the output is short.

Falls back to a static sentence on failure. The app never degrades visibly over a single feature.

### Nominatim
Free OpenStreetMap geocoding. Converts `lat/lng` → readable city name. No key required. Rate limit of 1 req/sec is more than sufficient for a single location lookup at app launch.

---

## Dependency Injection

Hilt throughout. Three modules:

**`NetworkModule`** — provides `Retrofit`, `OkHttp`, `OpenMeteoService`, `ClaudeApiService`

**`RepositoryModule`** — binds `WeatherRepository` interface to implementation

**`DataStoreModule`** — provides the `DataStore<Preferences>` singleton

---

## Error Handling

Every repository function returns `Result<T>`. Use cases unwrap and re-emit as domain errors. ViewModels catch and surface as `UiState.Error`. Screens show a quiet error state — no crash dialogs, no stack traces.

Network failures degrade gracefully. Cached data is shown when available.

---

— **waleedahmedja**
