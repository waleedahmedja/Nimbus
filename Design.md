# Design System

Nimbus is designed around one constraint: **nothing should feel irregular.**

Light mode, dark mode, morning, midnight, rain, clear sky — every state of the app should feel like it belongs to the same considered whole. Not a theme swap. A continuous design system that adapts.

---

## Design philosophy

**Calm over clever.** The app speaks once and means it. No repeated nudges, no rotating personality. The craft carries the experience.

**System-native.** Nimbus follows Android's system theme automatically. Light mode feels like daylight. Dark mode earns the OLED. Neither is an afterthought.

**Restraint as a feature.** Every element earns its place. If removing it doesn't break understanding, it's removed.

---

## Colour palette

### Dark mode (OLED-first)

| Token | Hex | Usage |
|---|---|---|
| `Background` | `#000000` | True black — full OLED |
| `Surface` | `#0D0D0D` | Cards, bottom sheets |
| `SurfaceVariant` | `#1A1A1A` | Secondary surfaces |
| `OnBackground` | `#F5F5F5` | Primary text |
| `OnSurface` | `#CCCCCC` | Secondary text |
| `OnSurfaceMuted` | `#666666` | Tertiary, metadata |
| `AccentYellow` | `#FFD60A` | Temperature high, key highlights |
| `AccentBlue` | `#4FC3F7` | Temperature low, precipitation |
| `AccentRed` | `#FF6B6B` | Alerts only — used sparingly |
| `Outline` | `#2A2A2A` | Dividers, borders |

### Light mode

| Token | Hex | Usage |
|---|---|---|
| `Background` | `#F8F9FA` | Soft off-white — not blinding |
| `Surface` | `#FFFFFF` | Cards, bottom sheets |
| `SurfaceVariant` | `#F0F0F0` | Secondary surfaces |
| `OnBackground` | `#111111` | Primary text |
| `OnSurface` | `#444444` | Secondary text |
| `OnSurfaceMuted` | `#999999` | Tertiary, metadata |
| `AccentYellow` | `#D4A017` | Temperature high (darkened for contrast) |
| `AccentBlue` | `#0288D1` | Temperature low, precipitation |
| `AccentRed` | `#D32F2F` | Alerts only |
| `Outline` | `#E0E0E0` | Dividers, borders |

### Sky canvas palette — dark mode

The canvas ignores the flat palette above and uses its own time-aware gradient system. See [SKYCANVAS.md](SKYCANVAS.md) for the full spec.

| Time of day | Gradient top | Gradient bottom |
|---|---|---|
| Night (0–4am) | `#000005` | `#0A0A1A` |
| Pre-dawn (4–5am) | `#0A0A1A` | `#1A1040` |
| Dawn (5–6:30am) | `#1A1040` | `#8B4513` |
| Sunrise (6:30–7:30am) | `#FF6B35` | `#FFB347` |
| Morning (7:30–11am) | `#1565C0` | `#42A5F5` |
| Midday (11am–2pm) | `#0D47A1` | `#1E88E5` |
| Afternoon (2–5pm) | `#1565C0` | `#64B5F6` |
| Golden hour (5–7pm) | `#E65100` | `#FFB74D` |
| Dusk (7–8pm) | `#4A148C` | `#E91E63` |
| Evening (8–10pm) | `#1A0A2E` | `#311B92` |
| Night (10pm–0am) | `#000005` | `#0D0D1A` |

### Sky canvas palette — light mode

Same time structure, lifted and softened. Canvas never goes pure white.

| Time of day | Gradient top | Gradient bottom |
|---|---|---|
| Morning | `#90CAF9` | `#E3F2FD` |
| Midday | `#42A5F5` | `#BBDEFB` |
| Golden hour | `#FFB74D` | `#FFECB3` |
| Dusk | `#CE93D8` | `#F8BBD0` |
| Night | `#283593` | `#3949AB` |

---

## Typography

Font pairing: **DM Serif Display** (display) + **DM Sans** (body).

DM Serif carries weight without aggression. DM Sans is clean without being sterile. Together they read like a well-designed magazine — editorial, not corporate.

```kotlin
// Type.kt scale

val NimbusTypography = Typography(
    // The big temperature number
    displayLarge  = TextStyle(
        fontFamily = DmSerifDisplay,
        fontWeight = FontWeight.Normal,  // thin weight at large size
        fontSize   = 96.sp,
        letterSpacing = (-2).sp
    ),
    // Location name, section headers
    displayMedium = TextStyle(
        fontFamily = DmSans,
        fontWeight = FontWeight.Light,
        fontSize   = 18.sp,
        letterSpacing = 1.5.sp
    ),
    // Hourly time labels, day names
    titleMedium   = TextStyle(
        fontFamily = DmSans,
        fontWeight = FontWeight.Medium,
        fontSize   = 14.sp,
        letterSpacing = 0.5.sp
    ),
    // Daily sentence, body copy
    bodyLarge     = TextStyle(
        fontFamily = DmSans,
        fontWeight = FontWeight.Normal,
        fontSize   = 16.sp,
        lineHeight  = 24.sp
    ),
    // Air quality badge, metadata
    labelSmall    = TextStyle(
        fontFamily = DmSans,
        fontWeight = FontWeight.Medium,
        fontSize   = 11.sp,
        letterSpacing = 0.8.sp
    ),
    // waleedahmedja signature
    labelMedium   = TextStyle(
        fontFamily = JetBrainsMono,      // monospaced for the craftsman mark
        fontWeight = FontWeight.Normal,
        fontSize   = 12.sp,
        letterSpacing = 1.2.sp
    )
)
```

---

## Spacing scale

Based on an 8dp grid. Nothing lives between grid points.

| Token | Value | Usage |
|---|---|---|
| `Spacing.xs` | 4dp | Icon padding, tight gaps |
| `Spacing.sm` | 8dp | Inner card padding |
| `Spacing.md` | 16dp | Standard component padding |
| `Spacing.lg` | 24dp | Section spacing |
| `Spacing.xl` | 32dp | Screen-level margins |
| `Spacing.xxl` | 48dp | Large section breaks |
| `Spacing.xxxl` | 64dp | Top of home screen sky breathing room |

---

## Shape system — squircle tokens

Every interactive surface uses a squircle corner. Not a pill, not a rectangle. The squircle sits between them and feels more considered than either.

```kotlin
object NimbusShapes {
    val ExtraSmall = RoundedCornerShape(8.dp)    // Air quality badge
    val Small      = RoundedCornerShape(12.dp)   // Rain timeline segments
    val Medium     = RoundedCornerShape(16.dp)   // Cards, daily sentence
    val Large      = RoundedCornerShape(24.dp)   // Bottom sheets
    val ExtraLarge = RoundedCornerShape(32.dp)   // Buttons — the signature squircle
    val Full       = CircleShape                 // Icon containers only
}
```

The `ExtraLarge` token is the Nimbus button. Every tappable surface in the app that deserves emphasis uses it. Open the app for two minutes and your eye starts to expect it.

---

## Elevation & depth

Dark mode: depth is expressed through **brightness**, not shadow. A surface that's more important is slightly brighter — `#0D0D0D` → `#1A1A1A` → `#252525`. Shadows are nearly invisible in dark OLED — they're expensive and often wrong.

Light mode: standard Material elevation shadows, subtle. `shadowElevation = 2.dp` maximum for cards. The sky canvas is always the most visually prominent element — nothing competes with it.

---

## Motion principles

**Purposeful, never decorative.** Every animation communicates something — loading, transition, state change. Nothing loops for aesthetics alone.

**Sky canvas** — the exception. Clouds drift continuously. The sun/moon arc moves in real time. This is ambient motion. It earns the exception because it mirrors the real world.

**Durations:**

| Type | Duration | Easing |
|---|---|---|
| Micro (badge, chip state change) | 100ms | FastOutLinearIn |
| Standard (card expand, screen transition) | 250ms | FastOutSlowIn |
| Expressive (sky palette transition on condition change) | 800ms | EaseInOut |
| Ambient (cloud drift, arc movement) | Continuous | Linear |

---

## Iconography

No icon library. Nimbus uses a minimal set of hand-specified SVG paths compiled as `ImageVector` in Compose. The icon set is weather-specific, consistent in stroke weight (1.5dp), and never filled — always outlined.

Icons used: sun, moon, cloud, rain drop, snowflake, wind, fog, UV index, compass.

Everything else is expressed through typography and colour, not icons.

---

## The `waleedahmedja` signature

```kotlin
@Composable
fun WaleedSignature() {
    Text(
        text = "waleedahmedja",
        style = MaterialTheme.typography.labelMedium,
        color = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.25f),
        modifier = Modifier
            .fillMaxWidth()
            .padding(bottom = Spacing.xl),
        textAlign = TextAlign.Center
    )
}
```

Alpha 0.25 in dark mode. Slightly higher — 0.35 — in light mode where black text on white is naturally lower contrast. It's there for people who look. Not everyone will. That's the point.

---

## Do and don't

**Do:**
- Let the sky canvas breathe — never clip it, never overlap it with UI chrome
- Use `AccentYellow` only for temperature highs and essential highlights — it earns its meaning through scarcity
- Follow the 8dp grid without exception
- Use the squircle token for every button, every time

**Don't:**
- Add a coloured status bar or navigation bar — edge to edge, always transparent
- Use bold weight for anything other than data values that need emphasis
- Show more than one alert or nudge at a time
- Add a loading spinner — use a skeleton shimmer or let the sky canvas render first while data loads

---

— **waleedahmedja**
