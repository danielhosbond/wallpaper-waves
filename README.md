# Wavy Wallpaper Creator

A browser-based wallpaper generator that produces layered organic wave compositions. Design, preview, and export at 8K resolution — no build step, no dependencies, no server. Just open `index.html`.

## Getting started

Download or clone the repository, then open `index.html` in any modern browser. No installation required.

## Layout

The interface is divided into three columns:

- **Left panel** — canvas settings, wave count, smoothing, randomize, and presets
- **Canvas** — live preview that updates on every change
- **Right panel** — per-wave color swatches and shape/shadow controls

## Left panel

### Canvas ratio

Choose the aspect ratio for the preview and export. Available options:

| Ratio | Export resolution |
|-------|------------------|
| 16:9  | 7680 × 4320      |
| 4:3   | 5760 × 4320      |
| 1:1   | 4320 × 4320      |
| 21:9  | 7560 × 3240      |
| 9:16  | 4320 × 7680      |

The preview scales to fit the available space. The active ratio is highlighted.

### Wave count

A slider from 2 to 10 that controls how many wave layers are drawn. Increasing the count adds new layers at the bottom with interpolated colors. Decreasing it removes from the bottom.

### Smoothing

Controls the curvature of wave edges (0–100). At 0 the waves are sharp and angular. At 100 they use fully smooth cubic bezier curves. Values in between blend between the two.

### Randomize waves

Assigns new random values for amplitude, frequency, and phase shift to every wave. Colors are not affected. Each click uses a different seed, so results never repeat.

### Presets

15 one-click presets divided into three groups:

**Original**
- **Sunset** — warm red-orange sky fading through teal to deep blue
- **Ocean** — pale sky blue cascading to midnight navy
- **Dusk** — deep violet to warm amber, dark-to-light
- **Forest** — light sage descending to deep woodland green
- **Sand** — cream and warm tan fading to brown

**Pastel**
- **Rose** — blush white through soft pink to deep mauve
- **Mint** — near-white through pale mint to deep forest teal
- **Peach** — cream through warm apricot to terracotta
- **Lilac** — soft white through periwinkle and lavender to deep plum
- **Meadow** — cool white through pale sage to rich grass green

**Bold / Dark**
- **Aurora** — near-black night sky through dark teal to vivid lime glow
- **Inferno** — charred black through blood red and burning orange to flame yellow
- **Abyss** — void black through midnight blue to deep-sea turquoise
- **Ember** — ash black through dark brown and sienna to warm amber
- **Nebula** — deep space through dark violet and magenta to hot pink

Applying a preset sets the wave count, all colors, and resets wave shape parameters to clean defaults.

## Right panel

### Wave colors

A stacked list of color swatches — one for the background fill and one per wave layer. Click any swatch to open the system color picker. The hex value updates live as you pick.

- **Background** — fills the entire canvas before any waves are drawn
- **Wave 1 through N** — each wave's fill color, from topmost to bottommost

### Wave shape controls

Each wave has its own collapsible group with four parameters:

| Control | Range | Effect |
|---------|-------|--------|
| Amplitude | 1–25 | Vertical height of the wave peaks as a percentage of canvas height |
| Frequency | 1–8 | Number of full wave cycles across the canvas width |
| Position | 5–95% | Vertical baseline of the wave as a percentage of canvas height |

### Shadow controls

Each wave has an independent shadow that follows its exact curve. Enable or disable it with the checkbox. When enabled:

| Control | Range | Effect |
|---------|-------|--------|
| Blur | 0–100 | Spread of the shadow beneath the wave crest |
| Opacity | 0–100% | Darkness of the shadow |

Shadow values are stored as fractions of canvas height, so they render identically at preview size and full 8K export resolution.

## Topbar actions

### Reset

Restores the app to its default state: Sunset preset, 16:9 ratio, smoothing at 80, and shadows enabled on all waves.

### Import

Opens a file picker and loads a previously exported `.json` configuration file. All state is restored including colors, wave shapes, shadow settings, and canvas ratio.

### Export

Downloads the current configuration as a `wavy-wallpaper.json` file. This file can be re-imported at any time to continue working on the design or share it with others.

### Export 8K PNG

Renders the composition to an offscreen canvas at the full export resolution for the active ratio (up to 7680 × 4320) and downloads it as `wavy-wallpaper-8k.png`. The button shows "Exporting..." while processing.

## Wave rendering

Each wave is generated from a sine function with a second harmonic added at 1.618× frequency for an organic, non-mechanical shape. The sampled points are connected using Catmull-Rom splines converted to cubic bezier curves, giving smooth continuous curves at any smoothing level.

Waves are drawn front to back. Each wave fills from its crest downward to the canvas edge, and the next wave's fill naturally overwrites the lower portion — creating clean color bands without any masking or clipping.

All drawing math uses fractions of canvas dimensions. The same function renders the preview and the 8K export, so the result is pixel-perfect at any resolution.

## Configuration file format

The exported JSON contains the full application state:

```json
{
  "ratio": "16:9",
  "waveCount": 4,
  "waveColors": ["#E8543A", "#F0923A", "#E8C04A", "#2BAA8A", "#1A5068"],
  "amplitude":   [0.12, 0.10, 0.09, 0.11],
  "frequency":   [3, 4, 3, 5],
  "phaseShift":  [0, 0.4, 1.1, 2.0],
  "verticalPos": [0.20, 0.38, 0.55, 0.72],
  "smoothing": 80,
  "seed": 42,
  "shadowEnabled": [true, true, true, true],
  "shadowBlur":    [35, 35, 35, 35],
  "shadowOpacity": [40, 40, 40, 40]
}
```

`waveColors` always has `waveCount + 1` entries. Index 0 is the background. Amplitude, frequency, phaseShift, verticalPos, and the three shadow arrays each have `waveCount` entries. Shadow values from files saved before the shadow feature was introduced are backfilled with defaults on import.
