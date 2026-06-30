# Nexus

A single-file, Bloomberg Terminal–style financial dashboard built in vanilla JavaScript. No backend, no build step, no framework — open the HTML file and it runs.

![Nexus Terminal](https://img.shields.io/badge/status-active-brightgreen) ![No Build](https://img.shields.io/badge/build-none-blue) ![Dependencies](https://img.shields.io/badge/dependencies-CDN%20only-orange)

## What this is

Nexus is a 29-module market terminal covering equities, options, fixed income, commodities, crypto, portfolio analytics, sentiment, and geopolitical risk. Everything — markup, styles, and logic — lives in one `.html` file. There is no Python backend, no Node server, no database. The "architecture" is a browser tab.

This makes it trivially portable: download the file, double-click it, and the terminal boots. It also makes it a useful teaching artifact for demonstrating that a serious-looking financial dashboard doesn't require a server stack to be functional — most of what people associate with a "terminal" is UI density and data modeling, not backend complexity.

## Why no backend

This was a deliberate choice, not a missing piece:

- **Zero deployment friction.** No server to provision, no API to keep alive, no database migrations. The file is the artifact.
- **Demo-data by default, live-data when you want it.** Every module ships with a realistic synthetic data generator so the terminal is fully interactive out of the box. Swapping in a real market data API is a one-line change per module (see [Connecting Real Data](#connecting-real-data) below).
- **Forces good separation of concerns anyway.** Even without a server, the file is organized so that data fetching, data shaping, and rendering are distinct functions — which is what makes it easy to point at a real backend later if you build one.

If you do want a real backend (e.g. to cache API responses, manage rate limits, or add auth), the natural shape would be: a thin Python (FastAPI/Flask) proxy that wraps Polygon/Alpha Vantage/FRED/CoinGecko, normalizes the JSON, and serves it to this same frontend unchanged. That isn't implemented here, but the `nexusFetch()` pattern described below is designed to make that swap painless.

## Quick start

```bash
git clone https://github.com/LordXapose/nexus.git
cd nexus
open Nexus_v5.html   # macOS
# or just double-click the file in Finder / Explorer
```

No `npm install`. No `pip install`. No `.env` file required to see the UI working with demo data.

## Architecture

### Single-file structure

```
Nexus_v5.html
├── <style>   ~500 lines   — CSS custom properties (design tokens), layout, component classes
├── <body>    ~3300 lines  — 29 module panels, each a <div class="module" id="...">
└── <script>  ~2500 lines  — state, utilities, per-module init/render functions, boot sequence
```

### Module system

Every screen in Nexus is a `div.module` element. Exactly one is visible at a time via a `.active` class toggle. Navigation buttons in the top bar call a single router:

```js
function switchModule(id, btn) {
  document.querySelectorAll('.module').forEach(m => m.classList.remove('active'));
  document.querySelectorAll('.mtab').forEach(b => b.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  if (btn) btn.classList.add('active');

  // lazy init — each module only builds its DOM/charts the first time it's opened
  if (id === 'earnings' && !earningsInit) {
    setTimeout(() => { initEarnings(); earningsInit = true; }, 80);
  }
  // ...repeated for all 29 modules
}
```

Lazy initialization means the terminal boots instantly regardless of module count — nothing renders until you click it, and each module only ever initializes once per session (tracked with a boolean flag like `earningsInit`).

### Design system

All visual styling routes through CSS custom properties so the whole terminal can be re-themed by changing a handful of values:

```css
--bg, --bg2, --bg3, --bg4, --bg5      /* background depth scale */
--amber, --green, --red, --blue, --purple   /* semantic accent colors */
--text, --text2, --text3, --text4     /* text hierarchy */
--border, --border2, --border3        /* dividers */
```

Typography: `Orbitron` for display/headers, `Share Tech Mono` for tabular UI — both loaded from Google Fonts via CDN.

### Shared utilities

A small set of helper functions is reused across every module to keep the code consistent:

| Function | Purpose |
|---|---|
| `rnd(a, b)` / `rndInt(a, b)` | Random float/int generators for synthetic data |
| `pick(arr)` | Random array element |
| `mkChart(id, config)` | Wraps Chart.js — destroys any existing chart on that canvas before creating a new one, so re-rendering a module never leaks chart instances |
| `mkKpi(el, items)` | Renders a row of KPI tiles from a `[label, value, className]` array |
| `drow(label, value, className)` | Renders a single label/value data row (used in every sidebar panel) |
| `fmtM(n)` | Formats large numbers as `$1.2M` / `$3.4B` |
| `nexusFetch(url, fallback)` | Fetches a real API with a 5-second timeout, falling back to demo data automatically if the request fails or no key is configured |
| `pushAlert(type, title, body)` | Pushes a toast-style alert into the global alert feed |

### Data flow per module

Every module follows the same three-part pattern:

1. **`initModuleName()`** — runs once on first visit. Generates or fetches the dataset, computes KPIs, and calls the initial render.
2. **`renderModuleName()`** / **`filterX()`** / **`setXMode()`** — re-renders the module's DOM and charts in response to filter buttons, dropdowns, or input changes, without re-fetching data.
3. **Chart.js canvases** — each chart lives in a named `<canvas>` and is built/rebuilt through `mkChart()`.

This means adding a new module is mechanical: write an `init`, optionally a `render`/`filter`, drop a `div.module` panel in the body, add one nav button, and register one line in `switchModule()`.

## Modules (29 total, across 10 navigation groups)

**Home** — Overview dashboard with live market summary cards

**Markets** — Dashboard (25-asset live ticker grid), Order Book, News & Macro

**Global Tracking** — All Traffic (Leaflet map), Aircraft tracking, Vessel tracking

**Risk & Analytics** — Aladdin-style Risk Overview, Stress Testing, Compliance monitoring, Monte Carlo simulation

**New Modules** — Options Chain (live Greeks, IV), Dark Pool flow tape, Macro Heat Map (20 countries), Correlation Lab (N×N matrix)

**Intelligence** — Earnings Calendar, Short Interest / Squeeze Monitor, Insider Transactions (SEC Form 4 style), Sentiment Dashboard (Fear & Greed, VIX term structure, put/call ratio, AAII survey), Social Sentiment / Trending Tickers, Sector Rotation (with an economic cycle clock)

**Fixed Income** — US Treasury Yield Curve (with historical overlays), Bond Screener, Credit Spreads (IG/HY OAS vs equity drawdown)

**Commodities** — Energy/Metals/Agriculture dashboard, BTC On-Chain Analytics (MVRV, NVT, exchange netflow), Futures Curve (contango/backwardation across 6 commodities)

**Portfolio** — Backtester (allocation vs benchmark, Sharpe/Sortino/Calmar, drawdown), Options P&L Calculator (8 strategies, Black-Scholes pricing), Tax Lot Tracker (FIFO/LIFO/HIFO, wash sale detection)

**Global** — Geopolitical Risk Monitor (12 countries, click-through detail), Global Trade Flow corridors and chokepoints, FX Intervention Tracker

## Connecting real data

Every module that pulls market data is set up to call `nexusFetch()` against a real provider and fall back to its synthetic generator automatically if no API key is configured. To go live, edit the config block near the top of the `<script>` tag:

```js
const NEXUS_API = {
  polygon: 'YOUR_KEY',       // polygon.io — equities, options
  alphavantage: 'YOUR_KEY',  // alphavantage.co — equities, fundamentals
  coingecko: '',             // no key required — crypto
  fred: 'YOUR_KEY',          // fred.stlouisfed.org — yields, macro series
};
```

Each module has a commented-out real API call directly above its mock data generator — uncomment the real call, delete (or keep as a fallback) the mock line, and that module goes live. No other code changes required.

## Tech stack

- **Vanilla JavaScript** (ES6+, no framework, no transpilation)
- **[Chart.js 4.4.1](https://www.chartjs.org/)** — all line/bar/doughnut charts, loaded via CDN
- **[Leaflet 1.9.4](https://leafletjs.com/)** — map-based tracking modules, loaded via CDN
- **Google Fonts** — Orbitron, Share Tech Mono
- **No build tooling** — no Webpack, no Vite, no npm dependencies at all

## Project status

This is an active personal project, primarily a portfolio piece demonstrating financial domain modeling (options pricing, fixed income math, portfolio risk metrics) and dense, information-rich UI engineering without backend complexity getting in the way. Contributions, issues, and forks welcome.

## License

MIT — do whatever you want with it.
