# Architecture

Nimbus follows a unidirectional data flow pattern — MVVM with StateFlow — consistent across all layers. The same architecture used in Score247 and Friction V2. Proven, boring in the best way.

---

## Guiding principles

**One source of truth.** `WeatherRepository` owns all data. ViewModels observe it. Screens observe ViewModels. Nothing reaches across layers.

**Dumb screens.** UI files contain zero business logic. They render state. They emit events. That's all.

**Minimal state.** Only what the UI actually needs lives in `UiState`. No raw DTOs, no API models leaking into the presentation layer.

**Fail gracefully.** Every network call is wrapped. Every `UiState` has an error case. The app never crashes silently.

---

## Layer map

```
┌─────────────────────────────────────┐
│           UI Layer                  │
│  Screens (Compose) + Components     │
│  SkyCanvas, RainTimeline, Spline    │
└────────────────┬────────────────────┘
                 │ observes
┌────────────────▼────────────────────┐
│        Presentation Layer           │
│  ViewModels + UiState (StateFlow)   │
└────────────────┬────────────────────┘
                 │ calls
┌────────────────▼────────────────────┐
│          Domain Layer               │
│  Use Cases + Clean Domain Models    │
└────────────────┬────────────────────┘
                 │ calls
┌────────────────▼────────────────────┐
│           Data Layer                │
│  Repository → API + DataStore       │
└─────────────────────────────────────┘
```

---

## Full file structure

```
app/src/main/java/com/waleedahmedja/nimbus/
│
├── data/
│   ├── api/
│   │   ├── OpenMeteoService.kt          # Retrofit interface for weather API
│   │   ├── ClaudeApiService.kt          # Retrofit interface for AI sentence
│   │   └── dto/
│   │       ├── WeatherResponseDto.kt    # Raw Open-Meteo response
│   │       ├── HourlyDto.kt             # Hourly block from API
│   │       ├── DailyDto.kt              # Daily block from API
│   │       └── ClaudeResponseDto.kt     # Claude API response shape
│   │
│   ├── repository/
│   │   └── WeatherRepository.kt         # Coordinates API + cache + prefs
│   │
│   └── datastore/
│       └── UserPreferences.kt           # Units (°C/°F, km/h/mph), cached sentence
│
├── domain/
│   ├── model/
│   │   ├── Weather.kt                   # Current conditions
│   │   ├── HourlyForecast.kt            # Single hour data point
│   │   ├── DailyForecast.kt             # Single day data point
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
│       └── SettingsScreen.kt            # Units toggle only
│
├── ui/
│   ├── canvas/
│   │   ├── SkyCanvas.kt                 # Root canvas composable
│   │   ├── SkyPalette.kt                # Time + condition → colour mapping
│   │   ├── CloudLayer.kt                # Animated cloud renderer
│   │   └── SunMoonArc.kt                # Solar/lunar arc drawing
│   │
│   ├── components/
│   │   ├── TemperatureDisplay.kt        # The large architectural number
│   │   ├── RainTimeline.kt              # Minute-by-minute precipitation bar
│   │   ├── WeekSpline.kt                # 7-day temperature spline curve
│   │   ├── DailySentenceCard.kt         # The AI-generated daily line
│   │   ├── AirQualityBadge.kt           # Good / Fair / Poor badge
│   │   ├── SunriseSunsetArc.kt          # Arc with current solar position
│   │   └── WaleedSignature.kt           # Craftsman's mark at scroll bottom
│   │
│   └── theme/
│       ├── Theme.kt                     # MaterialTheme wrapper, system-adaptive
│       ├── Color.kt                     # Full palette, light + dark
│       ├── Type.kt                      # Typography scale
│       └── Shape.kt                    # Squircle shape tokens
│
├── util/
│   ├── SolarCalculator.kt               # Sunrise, sunset, current sun position (0.0–1.0)
│   ├── LunarCalculator.kt               # Moon phase (0–7)
│   └── WeatherMapper.kt                 # DTO → domain model mapping
│
├── di/
│   ├── NetworkModule.kt                 # Hilt: Retrofit, OkHttp
│   ├── RepositoryModule.kt              # Hilt: WeatherRepository binding
│   └── DataStoreModule.kt              # Hilt: DataStore instance
│
└── NimbusApp.kt                         # Application class, Hilt entry point
```

---

## Data flow — current weather

```
HomeScreen
  └── observes HomeViewModel.uiState: StateFlow<HomeUiState>
        └── HomeViewModel.init
              └── GetCurrentWeatherUseCase()
                    └── WeatherRepository.getCurrentWeather()
                          ├── OpenMeteoService.getForecast()   [Retrofit]
                          └── UserPreferences.getUnits()       [DataStore]
```

---

## Data flow — daily sentence

Generated once per day. Cached in DataStore so it survives app restarts.

```
HomeViewModel.init
  └── GenerateDailySentenceUseCase()
        └── WeatherRepository.getDailySentence()
              ├── UserPreferences.getCachedSentence()     → return if today's date matches
              └── ClaudeApiService.generate(prompt)       → cache + return if stale
```

---

## UiState pattern

Every screen follows the same sealed structure:

```kotlin
sealed interface HomeUiState {
    data object Loading : HomeUiState
    data class Success(
        val weather: Weather,
        val hourly: List<HourlyForecast>,
        val daily: List<DailyForecast>,
        val dailySentence: String,
        val sunPosition: Float,       // 0.0 (sunrise) → 1.0 (sunset)
        val moonPhase: Int,           // 0–7
        val skyCondition: SkyCondition,
        val isDark: Boolean           // mirrors system theme for canvas
    ) : HomeUiState
    data class Error(val message: String) : HomeUiState
}
```

---

## Shape system — squircle tokens

All interactive surfaces use squircle corners via `Shape.kt`. Consistent with the design language across the app.

```kotlin
object NimbusShapes {
    val ExtraSmall = RoundedCornerShape(8.dp)    // badges, chips
    val Small      = RoundedCornerShape(12.dp)   // timeline segments
    val Medium     = RoundedCornerShape(16.dp)   // cards
    val Large      = RoundedCornerShape(24.dp)   // bottom sheets
    val ExtraLarge = RoundedCornerShape(32.dp)   // buttons (squircle)
    val Full       = CircleShape                 // icon containers
}
```

The `ExtraLarge` token is the Nimbus button signature. Every tappable button in the app uses it. Consistent, recognisable, slightly unexpected.

---

## API choices

### Open-Meteo
- Free. No API key. No rate limit for reasonable usage.
- Returns current conditions, hourly (48h), and daily (7d) in one call.
- Accuracy on par with commercial providers for most regions.
- Endpoint used: `https://api.open-meteo.com/v1/forecast`

### Claude API (Haiku)
- One call per day maximum. Cached result served for 24h.
- Haiku chosen for speed and cost — the prompt is simple, the output is short.
- Falls back to a static sentence if the API call fails. The app never breaks over a sentence.

### Nominatim
- Free OpenStreetMap geocoding. Converts lat/lng → readable city name.
- No key required. Rate limit: 1 req/sec — more than enough for a single location lookup.

---

## Dependency injection

Hilt throughout. Three modules:

`NetworkModule` — provides `Retrofit`, `OkHttp`, `OpenMeteoService`, `ClaudeApiService`
`RepositoryModule` — binds `WeatherRepository` interface to its implementation
`DataStoreModule` — provides the `DataStore<Preferences>` singleton

---

## Error handling philosophy

Every repository function returns `Result<T>`. Use cases unwrap and re-emit as domain errors. ViewModels catch and surface as `UiState.Error`. Screens show a quiet error state — no crash dialogs, no stack traces visible to the user.

Network failures degrade gracefully: cached data is shown if available, with a subtle staleness indicator.

---

— **waleedahmedja**
