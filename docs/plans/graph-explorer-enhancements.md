# Graph Explorer Enhancements — Design Plan

## Context
The Graph Explorer is an existing prototype in the `davehomeassist.github.io` portfolio hub repo. It currently visualizes data (including personal Notion data) and needs two enhancements:
1. **Customizable color schemes** — a full theme editor giving users control over backgrounds, text, accents, borders, and bar/chart colors
2. **Sample datasets** — pre-built demo graphs around universal topics (science, sports) so the live site doesn't expose personal data
3. **Optional interactive tour** — guided walkthrough of sample datasets (non-forced)

> **Note:** I cannot access the portfolio hub repo from this session. This plan is a design blueprint. Implementation requires either access to that repo or the user sharing the graph explorer source files.

---

## 1. Full Theme Editor

### Architecture
Use CSS custom properties (like the cost calculator already does) so themes are just variable overrides.

#### Theme Data Structure
```javascript
const themes = {
  "midnight": {
    label: "Midnight",
    colors: {
      bgBase: "#0f172a",
      bgElevated: "#1e293b",
      bgSurface: "#334155",
      textPrimary: "#f1f5f9",
      textSecondary: "#cbd5e1",
      textMuted: "#94a3b8",
      accentPrimary: "#38bdf8",
      accentSecondary: "#a78bfa",
      accentTertiary: "#fb923c",
      border: "#475569"
    },
    chartPalette: ["#14b8a6", "#3b82f6", "#a78bfa", "#fb923c", "#22c55e", "#ec4899", "#facc15"]
  },
  "light": { ... },
  "high-contrast": { ... },
  "colorblind-safe": { ... },
  "ocean": { ... },
  "forest": { ... }
};
```

#### UI Components
- **Theme preset dropdown** — quick-switch between built-in themes
- **Color picker panel** (expandable) with sections:
  - **Background colors** — base, elevated surface, surface
  - **Text colors** — primary, secondary, muted
  - **Accent colors** — primary, secondary, tertiary
  - **Border color**
  - **Chart palette** — ordered list of colors used for data series
- **Live preview** — changes apply instantly as user picks colors
- **Save/Load** — persist custom themes to `localStorage`
- **Reset to default** button

#### Built-in Theme Presets (6-8)
| Theme | Style | Background | Accents |
|-------|-------|-----------|---------|
| Midnight | Current dark | Navy | Teal/Purple |
| Light | Clean white | White/Gray | Blue/Indigo |
| High Contrast | Accessibility | Black | Bold primaries |
| Colorblind Safe | Deuteranopia/Protanopia friendly | Dark | Okabe-Ito palette |
| Ocean | Cool tones | Deep blue | Aqua/Seafoam |
| Sunset | Warm tones | Dark charcoal | Orange/Coral/Gold |
| Forest | Natural | Dark green | Green/Brown/Gold |
| Neon | Vibrant | Near-black | Bright neon |

#### Implementation Approach
```javascript
function applyTheme(theme) {
  const root = document.documentElement;
  Object.entries(theme.colors).forEach(([key, value]) => {
    root.style.setProperty(`--color-${camelToKebab(key)}`, value);
  });
  // Update chart palette
  theme.chartPalette.forEach((color, i) => {
    root.style.setProperty(`--chart-color-${i}`, color);
  });
  localStorage.setItem('graphExplorerTheme', JSON.stringify(theme));
}
```

Chart colors should be referenced via `var(--chart-color-0)` etc. instead of hardcoded class names, making them fully dynamic.

---

## 2. Sample Datasets

### Approach
Ship pre-built JSON datasets that load into the graph explorer. Each dataset tells a story with real-world data that's publicly available (no personal data).

### Proposed Sample Datasets

#### Science
1. **"Solar System"** — Planet distances, diameters, orbital periods (bar + scatter)
2. **"Periodic Table Trends"** — Atomic radius, electronegativity across elements (line chart)
3. **"Global Temperature Anomaly"** — NASA/NOAA historical temperature data (time series)

#### Sports
4. **"Olympic Medal Counts"** — Top 10 countries, summer vs winter (grouped bar)
5. **"Premier League 2024-25"** — Goals scored, points, win rate (multi-series bar)
6. **"NBA Player Stats"** — Points, rebounds, assists for top players (radar/spider or bar)

#### General / Other
7. **"World Population"** — Population growth by continent over decades (stacked area / bar)
8. **"Programming Language Popularity"** — TIOBE/Stack Overflow trends (line chart)

### Dataset Format
```javascript
const sampleDatasets = {
  "solar-system": {
    title: "Solar System Comparison",
    description: "Compare planets by distance, size, and orbital characteristics",
    category: "Science",
    source: "NASA Planetary Fact Sheet",
    sourceUrl: "https://nssdc.gsfc.nasa.gov/planetary/factsheet/",
    chartType: "bar",  // suggested default visualization
    columns: ["Planet", "Distance from Sun (AU)", "Diameter (km)", "Orbital Period (years)"],
    data: [
      ["Mercury", 0.39, 4879, 0.24],
      ["Venus", 0.72, 12104, 0.62],
      // ...
    ]
  }
};
```

### UI for Sample Data
- **"Load Example" dropdown** in the graph explorer toolbar
- Grouped by category (Science / Sports / General)
- Each option shows title + one-line description
- Loading a sample populates the graph immediately
- Clear indicator: "Viewing sample data — [Dataset Name]"

---

## 3. Optional Interactive Tour

### Design
- **Not forced** — a "Take a Tour" button (not an auto-popup)
- Uses a lightweight spotlight/tooltip approach (no heavy library needed)
- Steps through 4-5 key features:
  1. "Choose a dataset or upload your own"
  2. "Switch chart types to explore different views"
  3. "Customize colors with the theme editor"
  4. "Hover/click data points for details"
  5. "Export your visualization"
- Dismissable at any step, remembers dismissal in `localStorage`

### Implementation
Simple CSS overlay + JS state machine — no dependency needed:
```javascript
const tourSteps = [
  { target: "#dataSelector", text: "Start by picking a dataset...", position: "bottom" },
  { target: "#chartType", text: "Try different chart types...", position: "right" },
  { target: "#themeEditor", text: "Customize your color scheme...", position: "left" },
];
```

---

## Verification
Since the graph explorer code is in another repo:
- Implementation will need to happen in the `davehomeassist.github.io` repo
- Test by opening the HTML file locally and:
  - Switching between all theme presets
  - Using the color picker to create a custom theme, refreshing to verify persistence
  - Loading each sample dataset and confirming correct rendering
  - Running the tour from start to finish and verifying dismissal persistence
  - Testing on mobile viewport sizes
