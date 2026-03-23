# Sky Canvas

The sky canvas is the signature piece of Nimbus. Everything else in the app is well-designed. The canvas is something different — it's the thing that makes someone pick up another person's phone and ask "wait, what app is this?"

This document explains how it works, why it works that way, and the maths behind the parts that need it.

---

## What it is

A full-bleed Jetpack Compose `Canvas` composable that renders a generative sky behind the entire home screen. It reacts to:

- **Time of day** — gradient shifts continuously through the day
- **Weather condition** — overlays and modifiers applied per `SkyCondition`
- **System theme** — light mode lifts and softens every palette; dark mode goes to OLED black
- **Solar position** — the sun or moon arc drawn at the correct point in the sky

It is not a stock image. It is not a Lottie animation. It is not a video. It is drawn, every frame, by the GPU via Compose's `DrawScope`.

---

## File structure

```
ui/canvas/
├── SkyCanvas.kt       # Root composable — coordinates all layers
├── SkyPalette.kt      # Maps (time, condition, isDark) → gradient colours
├── CloudLayer.kt      # Animated cloud renderer
└── SunMoonArc.kt      # Draws the solar/lunar arc and body
```

---

## SkyCanvas.kt — how it's composed

```
SkyCanvas (Box, fillMaxSize)
  ├── Layer 0: Sky gradient         (DrawScope — Brush.linearGradient)
  ├── Layer 1: Condition overlay    (DrawScope — semi-transparent wash)
  ├── Layer 2: Cloud layer          (Canvas — animated, offset-driven)
  ├── Layer 3: Sun/moon arc         (DrawScope — arc + circle/crescent)
  └── Layer 4: Stars (night only)   (DrawScope — random point scatter, seeded)
```

Each layer is drawn in order. Compose's `Canvas` uses `drawBehind` or a dedicated `Canvas` composable — the sky lives entirely behind the scrollable content column.

The scrollable content sits on top with a transparent background. As the user scrolls, the sky stays fixed (it's not inside the scroll). This is achieved with a `Box` layout — sky canvas fills the `Box`, scrollable column overlaid on top.

---

## SkyPalette.kt — the colour system

The palette function signature:

```kotlin
fun skyGradient(
    hour: Int,              // 0–23
    minute: Int,            // 0–59
    condition: SkyCondition,
    isDark: Boolean
): Pair<Color, Color>       // topColour, bottomColour
```

Time is treated as a continuous float (`hour + minute / 60f`) and mapped to gradient keyframes. Colours are linearly interpolated between keyframes so the transition is smooth — no visible jumps.

### Dark mode keyframes

```kotlin
val darkKeyframes = listOf(
    0.0f  to Pair(Color(0xFF000005), Color(0xFF0A0A1A)),  // midnight
    4.0f  to Pair(Color(0xFF000005), Color(0xFF0A0A1A)),  // deep night
    5.0f  to Pair(Color(0xFF0A0A1A), Color(0xFF1A1040)),  // pre-dawn
    6.5f  to Pair(Color(0xFF1A1040), Color(0xFF8B4513)),  // dawn
    7.0f  to Pair(Color(0xFFFF6B35), Color(0xFFFFB347)),  // sunrise
    8.0f  to Pair(Color(0xFF1565C0), Color(0xFF42A5F5)),  // morning
    12.0f to Pair(Color(0xFF0D47A1), Color(0xFF1E88E5)),  // midday
    15.0f to Pair(Color(0xFF1565C0), Color(0xFF64B5F6)),  // afternoon
    17.5f to Pair(Color(0xFFE65100), Color(0xFFFFB74D)),  // golden hour
    19.5f to Pair(Color(0xFF4A148C), Color(0xFFE91E63)),  // dusk
    21.0f to Pair(Color(0xFF1A0A2E), Color(0xFF311B92)),  // early night
    24.0f to Pair(Color(0xFF000005), Color(0xFF0A0A1A)),  // midnight
)
```

Interpolation:

```kotlin
fun interpolateKeyframes(timeFloat: Float, keyframes: List<Pair<Float, Pair<Color, Color>>>): Pair<Color, Color> {
    val before = keyframes.lastOrNull { it.first <= timeFloat } ?: keyframes.first()
    val after  = keyframes.firstOrNull { it.first > timeFloat } ?: keyframes.last()
    val t = if (after.first == before.first) 0f
            else (timeFloat - before.first) / (after.first - before.first)
    return Pair(
        lerp(before.second.first,  after.second.first,  t),
        lerp(before.second.second, after.second.second, t)
    )
}
```

### Condition modifiers

After the base gradient is resolved, a condition overlay is applied:

```kotlin
fun conditionOverlay(condition: SkyCondition, isDark: Boolean): Color {
    return when (condition) {
        SkyCondition.CLEAR  -> Color.Transparent
        SkyCondition.CLOUDY -> Color(0xFF808080).copy(alpha = if (isDark) 0.25f else 0.15f)
        SkyCondition.RAIN   -> Color(0xFF505060).copy(alpha = if (isDark) 0.40f else 0.25f)
        SkyCondition.STORM  -> Color(0xFF303040).copy(alpha = if (isDark) 0.55f else 0.40f)
        SkyCondition.SNOW   -> Color(0xFFB0C4DE).copy(alpha = if (isDark) 0.20f else 0.30f)
        SkyCondition.FOG    -> Color(0xFFA0A0A0).copy(alpha = if (isDark) 0.45f else 0.35f)
    }
}
```

This overlay is drawn as a full-canvas `drawRect` after the gradient. It desaturates and shifts the mood without replacing the underlying time-aware colour.

---

## CloudLayer.kt — animated clouds

Clouds are drawn as soft ellipses with a high `BlurMaskFilter` applied via `Paint`. They move left to right at a slow, continuous speed driven by `withInfiniteAnimationFrameMillis` or an `Animatable` offset.

```kotlin
data class Cloud(
    val x: Float,          // current x offset (0.0–1.5 * canvas width)
    val y: Float,          // fixed y (fraction of canvas height, 0.1–0.5)
    val scale: Float,      // 0.6–1.4
    val speed: Float,      // pixels per frame — very slow (0.1–0.4)
    val alpha: Float       // 0.05–0.25 in dark mode; 0.15–0.45 in light mode
)
```

Clouds are seeded deterministically from the current date — same clouds all day, different clouds tomorrow. This prevents jarring recomposition on every relaunch.

When a cloud exits the right edge, it's reset to just off the left edge with a new random y and scale. The pool size is fixed at 6 clouds. No more — the sky should feel open, not crowded.

Cloud visibility by condition:

| Condition | Cloud alpha multiplier |
|---|---|
| CLEAR | 0.3× (barely visible) |
| CLOUDY | 1.0× |
| RAIN | 1.2× (darker, denser) |
| STORM | 1.5× (near opaque) |
| SNOW | 0.8× |
| FOG | 2.0× (fog is just low cloud) |

---

## SunMoonArc.kt — solar and lunar position

### Solar position (0.0 → 1.0)

The sun's position in the sky is expressed as a float from 0.0 (sunrise) to 1.0 (sunset). The `SolarCalculator` utility computes this from local time, sunrise time, and sunset time.

```kotlin
fun sunPosition(
    currentTime: LocalTime,
    sunrise: LocalTime,
    sunset: LocalTime
): Float {
    val totalDayMinutes  = sunrise.until(sunset, ChronoUnit.MINUTES).toFloat()
    val elapsedMinutes   = sunrise.until(currentTime, ChronoUnit.MINUTES).toFloat()
    return (elapsedMinutes / totalDayMinutes).coerceIn(0f, 1f)
}
```

The arc is drawn as a half-circle path across the canvas width. The sun body is a filled circle positioned at `position` along the arc path.

```
Arc geometry:

  left edge                              right edge
  (sunrise)                              (sunset)
       *────────────────────────────────────*
        \              arc                /
         \                              /
          \          ☀ (at t=0.6)    /
           \                        /
            *──────────────────────*
```

The arc is drawn with `drawArc` using a sweep of 180°, from left to right. The sun's x/y on the arc:

```kotlin
val angle = Math.PI * sunPosition          // 0 → π
val arcCentreX = canvasWidth / 2f
val arcCentreY = canvasHeight * 0.7f       // arc baseline sits at 70% height
val arcRadius  = canvasWidth * 0.45f

val sunX = (arcCentreX - arcRadius * cos(angle)).toFloat()
val sunY = (arcCentreY - arcRadius * sin(angle)).toFloat()
```

### Lunar phase

Moon phase (0–7) is determined by `LunarCalculator` using the known new moon epoch and the synodic period (29.53 days).

```kotlin
fun moonPhase(date: LocalDate): Int {
    val knownNewMoon = LocalDate.of(2024, 1, 11)
    val daysSince = knownNewMoon.until(date, ChronoUnit.DAYS)
    val phase = ((daysSince % 29.53) / 29.53 * 8).toInt()
    return phase.coerceIn(0, 7)
}
```

Phase 0 = new moon (not drawn — dark sky). Phase 4 = full moon (full circle). Phases 1–3 and 5–7 draw a crescent using two overlapping arcs clipped to produce the correct illumination shape.

The moon replaces the sun arc at night. It follows the same arc geometry but positioned for its rise/set time. For V1, moon position is approximated as the inverse of the sun arc with an hour offset — accurate enough to feel right, not astronomically precise.

---

## Stars (night only)

Drawn as small points (radius 1–2dp) scattered across the upper 60% of the canvas. The scatter is seeded from the current date — same stars every night, different pattern each night. Alpha varies per star (0.3–0.9) to add depth.

Visible only when `hour < 5 || hour > 21`. Fade in/out over 30 minutes around those thresholds using `animateFloatAsState` tied to a boolean derived from the time.

Total star count: 120. Enough to feel like a sky. Not so many it looks like a screensaver.

---

## Performance considerations

The canvas recomposes only when its inputs change — time (updated every minute via a `LaunchedEffect` ticker), condition (changes with network data), or theme. It does not recompose every frame for static elements.

Cloud movement uses `withInfiniteAnimationFrameMillis` scoped to the cloud layer only. This invalidates only the cloud drawing commands, not the full composable tree.

The sky canvas targets 60fps on mid-range Android devices (Snapdragon 680+). On lower-end devices, cloud animation can be disabled via a flag in `UserPreferences` — though this is not exposed in the UI in V1.

---

— **waleedahmedja**
