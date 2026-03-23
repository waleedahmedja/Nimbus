# Sky Canvas

The sky canvas is the signature piece of Nimbus.

Everything else in the app is well-designed. The canvas is something different — it's the thing that makes someone pick up another person's phone and ask "wait, what app is this?"

This document explains how it works, why it works that way, and the maths behind the parts that need it.

---

## What It Is

A full-bleed Jetpack Compose `Canvas` composable that renders a generative sky behind the entire home screen.

It reacts to:
- **Time of day** — gradient shifts continuously through the day
- **Weather condition** — overlays applied per `SkyCondition`
- **System theme** — light mode lifts and softens every palette
- **Solar position** — the sun or moon drawn at the correct point in the sky

It is not a stock image. It is not a Lottie file. It is not a video. It is drawn, every frame, by the GPU via Compose's `DrawScope`.

---

## File Structure

```
ui/canvas/
├── SkyCanvas.kt       # Root composable — coordinates all layers
├── SkyPalette.kt      # Maps (time, condition, isDark) → gradient colours
├── CloudLayer.kt      # Animated cloud renderer
└── SunMoonArc.kt      # Draws the solar / lunar arc and body
```

---

## 1. SkyCanvas.kt — Layer Order

```
SkyCanvas (Box, fillMaxSize)
  ├── Layer 0: Sky gradient          DrawScope — Brush.linearGradient
  ├── Layer 1: Condition overlay     DrawScope — semi-transparent colour wash
  ├── Layer 2: Cloud layer           Canvas — animated, offset-driven
  ├── Layer 3: Sun / moon arc        DrawScope — arc path + circle or crescent
  └── Layer 4: Stars (night only)    DrawScope — deterministic point scatter
```

Each layer is drawn in order. The sky canvas lives entirely behind the scrollable content column. The scrollable content sits on top with a transparent background, fixed in place while the sky stays full-screen. This is achieved with a `Box` layout — sky canvas fills the `Box`, scrollable column overlaid.

---

## 2. SkyPalette.kt — The Colour System

```kotlin
fun skyGradient(
    hour: Int,
    minute: Int,
    condition: SkyCondition,
    isDark: Boolean
): Pair<Color, Color>   // topColour, bottomColour
```

Time is treated as a continuous float (`hour + minute / 60f`) and mapped through gradient keyframes. Colours are linearly interpolated between keyframes — no visible jumps, no hard cuts.

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

### Interpolation

```kotlin
fun interpolateKeyframes(
    timeFloat: Float,
    keyframes: List<Pair<Float, Pair<Color, Color>>>
): Pair<Color, Color> {
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

### Condition overlays

After the base gradient resolves, a condition-specific colour wash is drawn over the full canvas:

```kotlin
fun conditionOverlay(condition: SkyCondition, isDark: Boolean): Color = when (condition) {
    SkyCondition.CLEAR  -> Color.Transparent
    SkyCondition.CLOUDY -> Color(0xFF808080).copy(alpha = if (isDark) 0.25f else 0.15f)
    SkyCondition.RAIN   -> Color(0xFF505060).copy(alpha = if (isDark) 0.40f else 0.25f)
    SkyCondition.STORM  -> Color(0xFF303040).copy(alpha = if (isDark) 0.55f else 0.40f)
    SkyCondition.SNOW   -> Color(0xFFB0C4DE).copy(alpha = if (isDark) 0.20f else 0.30f)
    SkyCondition.FOG    -> Color(0xFFA0A0A0).copy(alpha = if (isDark) 0.45f else 0.35f)
}
```

---

## 3. CloudLayer.kt — Animated Clouds

Clouds are drawn as soft ellipses with a `BlurMaskFilter` applied via `Paint`. They move left to right at a slow, continuous speed.

```kotlin
data class Cloud(
    val x: Float,       // current x offset — 0.0 to 1.5× canvas width
    val y: Float,       // fixed y — fraction of canvas height, 0.1 to 0.5
    val scale: Float,   // 0.6 to 1.4
    val speed: Float,   // pixels per frame — 0.1 to 0.4 (very slow)
    val alpha: Float    // 0.05–0.25 dark / 0.15–0.45 light
)
```

Clouds are seeded deterministically from the current date. Same clouds all day. Different clouds tomorrow. This prevents jarring recomposition on every relaunch.

When a cloud exits the right edge it resets to just off the left edge with a new random y and scale. Pool size is fixed at 6 clouds. The sky should feel open, not crowded.

**Cloud visibility by condition:**

| Condition | Alpha multiplier |
|---|---|
| CLEAR | 0.3× — barely there |
| CLOUDY | 1.0× — full visibility |
| RAIN | 1.2× — darker, denser |
| STORM | 1.5× — near opaque |
| SNOW | 0.8× |
| FOG | 2.0× — fog is just low cloud |

---

## 4. SunMoonArc.kt — Solar and Lunar Position

### Solar position — 0.0 to 1.0

The sun's position is a float: 0.0 at sunrise, 1.0 at sunset. Computed by `SolarCalculator`.

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

### Arc geometry

```
  left edge (sunrise)                   right edge (sunset)
        *──────────────────────────────────*
         \             arc               /
          \                            /
           \        ☀ (at t=0.6)    /
            \                      /
             *────────────────────*
```

The arc is a half-circle swept 180° across the canvas width. The sun's x/y on the arc:

```kotlin
val angle      = Math.PI * sunPosition
val arcCentreX = canvasWidth / 2f
val arcCentreY = canvasHeight * 0.7f    // arc baseline at 70% height
val arcRadius  = canvasWidth * 0.45f

val sunX = (arcCentreX - arcRadius * cos(angle)).toFloat()
val sunY = (arcCentreY - arcRadius * sin(angle)).toFloat()
```

### Lunar phase — 0 to 7

```kotlin
fun moonPhase(date: LocalDate): Int {
    val knownNewMoon = LocalDate.of(2024, 1, 11)
    val daysSince    = knownNewMoon.until(date, ChronoUnit.DAYS)
    val phase        = ((daysSince % 29.53) / 29.53 * 8).toInt()
    return phase.coerceIn(0, 7)
}
```

Phase 0 = new moon (not drawn — dark sky). Phase 4 = full moon (filled circle). Phases 1–3 and 5–7 draw a crescent using two overlapping arcs clipped to the correct illumination shape.

The moon replaces the sun after sunset. It follows the same arc geometry, approximated from a rise/set offset. Accurate enough to feel right. Not an astronomy app.

---

## 5. Stars

Drawn as small points (radius 1–2dp) scattered across the upper 60% of the canvas. Scatter is seeded from the current date. Same positions every night, different each night.

Alpha varies per star from 0.3 to 0.9 — depth without a z-buffer.

Visible when `hour < 5 || hour > 21`. Fade in and out over 30 minutes around those thresholds using `animateFloatAsState`.

Total star count: 120. Enough to read as a sky. Not so many it looks like a screensaver.

---

## 6. Performance

The canvas recomposes only when its inputs change — time (updated every minute via `LaunchedEffect` ticker), condition (changes with network data), or theme. Static elements do not trigger per-frame recomposition.

Cloud movement uses `withInfiniteAnimationFrameMillis` scoped to the cloud layer only. Only cloud draw commands are invalidated each frame.

Target: 60fps on mid-range Android devices (Snapdragon 680 class and above).

On lower-end devices, cloud animation can be disabled via a flag in `UserPreferences`. This is not exposed in the V1 UI — it is a silent fallback that the system can apply automatically based on `DevicePerformanceClass`.

---

— **waleedahmedja**
