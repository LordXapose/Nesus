# NEXUS TERMINAL — Institutional-Grade Financial & Tracking Suite

**NEXUS TERMINAL** is a full‑screen, Bloomberg‑style web application that integrates:

- Real‑time market data (stocks, crypto, forex)
- Advanced order book with depth charts
- Portfolio management & P&L tracking
- Global asset tracking (ADS‑B aircraft + AIS vessels)
- Institutional risk analytics (VaR, stress testing, Monte Carlo, factor decomposition, compliance)
- AI‑powered analyst assistant
- News & macro economic calendar

> **⚠️ Demo / Mock Data** – All data is simulated for demonstration purposes. No API keys or backend required. Runs entirely in the browser.

---

## 📸 Features at a glance

| Module | Description |
|--------|-------------|
| **Home** | Quick overview with market ticker and feature cards. |
| **Market Dashboard** | Watchlist, interactive price charts, order book, sector heatmap, news feed, portfolio tracker, screener (25+ signals), FX/macro data. |
| **Order Book** | Level‑2 bid/ask ladder (20 levels), cumulative depth chart, time & sales tape, buy/sell pressure ratio. |
| **Global Tracking** | Live map with 80 aircraft (ADS‑B) and 120 vessels (AIS). Filter by type, search, alert status. Detailed side panels for each asset. |
| **Aircraft Tracker** | Dedicated aviation view: flight table, stats (altitude, speed, airline share, top routes), map with on‑time/delayed markers. |
| **Vessel Tracker** | Maritime view: cargo/tanker/container/bulk filters, IMO/MMSI/flag data, draft/tonnage/cargo manifest. |
| **Risk & Analytics (Aladdin‑style)** | Portfolio NAV, VaR 99%, Sharpe ratio, volatility, beta. Factor decomposition, stress scenarios (2008, COVID, rate hikes, etc.), Monte Carlo simulation (GBM/jump‑diffusion), compliance rule engine (48 rules, real‑time breaches), Brinson attribution, efficient frontier optimization. |
| **News & Macro** | News feed with impact tags, economic calendar, earnings surprises, central bank rate trackers. |
| **AI Analyst** | Sidebar chat with simulated Claude‑style responses about portfolio risk, compliance, and tracking data. |
| **Live Alerts** | Price thresholds, compliance breaches, vessel/aircraft alerts – pop‑up panel with notifications. |
| **Light/Dark Theme** | Toggle between dark and light themes. |

---

## 🚀 Getting Started

### Prerequisites
- A modern web browser (Chrome, Firefox, Edge, Safari)
- Internet connection (for CDN resources and optional AI API)

### Installation
1. **Clone or download** this repository.
2. **Open `index.html`** in your browser – no build step or server required.

```bash
git clone https://github.com/yourusername/nexus-terminal.git
cd nexus-terminal
open index.html
