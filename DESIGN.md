# Design System

**Effective version:** V1
**Last updated:** March 2026

Nimbus is designed around one constraint: nothing should feel irregular.

Light mode, dark mode, morning, midnight, rain, clear sky — every state of the app should feel like it belongs to the same considered whole. Not a theme swap. A continuous design system that adapts.

---

## Short Version

The sky is the interface. Everything else is placed inside it with restraint.

---

## Philosophy

**Calm over clever.** The app speaks once and means it. No repeated nudges, no rotating personality. The craft carries the experience.

**System-native.** Nimbus follows Android's system theme automatically. Light mode feels like daylight. Dark mode earns the OLED. Neither is an afterthought.

**Restraint as a feature.** Every element earns its place. If removing it doesn't break understanding, it's removed.

**Squircle consistency.** Every interactive surface uses the same corner token. Open the app twice and your eye internalises it without being told.

---

## 1. Colour Palette

### Dark Mode (OLED-first)

| Token | Hex | Usage |
|---|---|---|
| `Background` | `#000000` | True black — full OLED, every pixel off |
| `Surface` | `#0D0D0D` | Cards, bottom sheets |
| `SurfaceVariant` | `#1A1A1A` | Secondary surfaces |
| `OnBackground` | `#F5F5F5` | Primary text |
| `OnSurface` | `#CCCCCC` | Secondary text |
| `OnSurfaceMuted` | `#666666` | Tertiary, metadata, timestamps |
| `AccentYellow` | `#FFD60A` | Temperature highs, key highlights — used sparingly |
| `AccentBlue` | `#4FC3F7` | Temperature lows, precipitation |
| `AccentRed` | `#FF6B6B` | Alerts only — almost never appears |
| `Outline` | `#2A2A2A` | Dividers, borders |

### Light Mode

| Token | Hex | Usage |
|---|---|---|
| `Background` | `#F8F9FA` | Soft off-white — never blinding |
| `Surface` | `#FFFFFF` | Cards, bottom sheets |
| `SurfaceVariant` | `#F0F0F0` | Secondary surfaces |
| `OnBackground` | `#111111` | Primary text |
| `OnSurface` | `#444444` | Secondary text |
| `OnSurfaceMuted` | `#999999` | Tertiary, metadata |
| `AccentYellow` | `#D4A017` | Temperature highs — darkened for contrast on white |
| `AccentBlue` | `#0288D1` | Temperature lows, precipitation |
| `AccentRed` | `#D32F2F` | Alerts only |
| `Outline` | `#E0E0E0` | Dividers, borders |

`AccentYellow` is the only accent. It earns its meaning through scarcity. If everything is yellow, nothing is.

---

## 2. Sky Canvas Palette

The canvas ignores the flat palette above and uses its own time-aware gradient system.
Full technical specification in [SKYCANVAS.md](SKYCANVAS.md).

### Dark mode sky keyframes

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

### Light mode sky keyframes

Same time structure, lifted and softened. Never goes pure white.

| Time of day | Gradient top | Gradient bottom |
|---|---|---|
| Morning | `#90CAF9` | `#E3F2FD` |
| Midday | `#42A5F5` | `#BBDEFB` |
| Golden hour | `#FFB74D` | `#FFECB3` |
| Dusk | `#CE93D8` | `#F8BBD0` |
| Night | `#283593` | `#3949AB` |

---

## 3. Typography

Font pairing: **DM Serif Display** (display) + **DM Sans** (body) + **JetBrains Mono** (signature only).

DM Serif carries weight without aggression. DM Sans is clean without being sterile. Together they read like a considered magazine — editorial, not corporate. JetBrains Mono appears exactly once, for the `waleedahmedja` signature.

```kotlin
val NimbusTypography = Typography(
    displayLarge  = TextStyle(          // The temperature number
        fontFamily    = DmSerifDisplay,
        fontWeight    = FontWeight.Normal,
        fontSize      = 96.sp,
        letterSpacing = (-2).sp
    ),
    displayMedium = TextStyle(          // Location name
        fontFamily    = DmSans,
        fontWeight    = FontWeight.Light,
        fontSize      = 18.sp,
        letterSpacing = 1.5.sp
    ),
    titleMedium   = TextStyle(          // Hourly time labels, day names
        fontFamily    = DmSans,
        fontWeight    = FontWeight.Medium,
        fontSize      = 14.sp,
        letterSpacing = 0.5.sp
    ),
    bodyLarge     = TextStyle(          // Daily sentence, body copy
        fontFamily    = DmSans,
        fontWeight    = FontWeight.Normal,
        fontSize      = 16.sp,
        lineHeight    = 24.sp
    ),
    labelSmall    = TextStyle(          // Air quality badge, metadata
        fontFamily    = DmSans,
        fontWeight    = FontWeight.Medium,
        fontSize      = 11.sp,
        letterSpacing = 0.8.sp
    ),
    labelMedium   = TextStyle(          // waleedahmedja signature only
        fontFamily    = JetBrainsMono,
        fontWeight    = FontWeight.Normal,
        fontSize      = 12.sp,
        letterSpacing = 1.2.sp
    )
)
```

---

## 4. Spacing Scale

Based on an 8dp grid. Nothing lives between grid points.

| Token | Value | Usage |
|---|---|---|
| `Spacing.xs` | 4dp | Icon padding, tight gaps |
| `Spacing.sm` | 8dp | Inner card padding |
| `Spacing.md` | 16dp | Standard component padding |
| `Spacing.lg` | 24dp | Section spacing |
| `Spacing.xl` | 32dp | Screen-level margins |
| `Spacing.xxl` | 48dp | Large section breaks |
| `Spacing.xxxl` | 64dp | Top of home screen — sky needs to breathe |

---

## 5. Shape System

```kotlin
object NimbusShapes {
    val ExtraSmall = RoundedCornerShape(8.dp)     // Air quality badge
    val Small      = RoundedCornerShape(12.dp)    // Rain timeline segments
    val Medium     = RoundedCornerShape(16.dp)    // Cards, daily sentence container
    val Large      = RoundedCornerShape(24.dp)    // Bottom sheets
    val ExtraLarge = RoundedCornerShape(32.dp)    // Buttons — the Nimbus squircle
    val Full       = CircleShape                  // Icon containers only
}
```

`ExtraLarge` is the button. Every tappable surface in the app that deserves emphasis uses it. Consistent, recognisable, slightly unexpected.

---

## 6. Motion

**Purposeful, never decorative.** Every animation communicates something — loading, transition, state change. Nothing loops for aesthetics alone.

The sky canvas is the exception. Clouds drift continuously. The sun arc moves in real time. This is ambient motion. It mirrors the real world, which earns the exception.

| Type | Duration | Easing |
|---|---|---|
| Micro (badge state change) | 100ms | FastOutLinearIn |
| Standard (card expand, transition) | 250ms | FastOutSlowIn |
| Sky palette shift (condition change) | 800ms | EaseInOut |
| Ambient (cloud drift, arc movement) | Continuous | Linear |

---

## 7. Elevation and Depth

**Dark mode:** depth is expressed through brightness, not shadow. A more important surface is slightly brighter — `#0D0D0D` → `#1A1A1A` → `#252525`. Shadows are invisible on OLED and expensive to render.

**Light mode:** standard Material elevation shadows, kept subtle. `shadowElevation = 2.dp` maximum for cards. The sky canvas is always the most prominent element — nothing competes with it.

---

## 8. Layout Rules

- Edge to edge. Always. No coloured status bar, no coloured navigation bar.
- The sky canvas fills the screen. Scrollable content overlays it with a transparent background.
- No bottom navigation bar. Screens are reached through contextual scroll, not tab switching.
- The settings screen is the only screen without a sky canvas behind it.

---

## 9. The `waleedahmedja` Signature

```kotlin
@Composable
fun WaleedSignature() {
    Text(
        text      = "waleedahmedja",
        style     = MaterialTheme.typography.labelMedium,
        color     = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.25f),
        modifier  = Modifier
            .fillMaxWidth()
            .padding(bottom = Spacing.xl),
        textAlign = TextAlign.Center
    )
}
```

25% opacity in dark mode. 35% in light mode. Monospaced. Centred. It's there for people who scroll all the way down and look. Not everyone will. That's the point.

It is never removed. Never moved. Never restyled.

---

## Do and Don't

**Do:**
- Let the sky canvas breathe — never clip it, never overlap it with opaque UI chrome
- Use `AccentYellow` only for temperature highs and essential highlights
- Follow the 8dp grid without exception
- Use `NimbusShapes.ExtraLarge` for every button, every time

**Don't:**
- Add a loading spinner — use skeleton shimmer or let the sky render first
- Bold anything that isn't a data value needing emphasis
- Show more than one piece of ambient information at a time
- Introduce a second accent colour

---

— **waleedahmedja**
