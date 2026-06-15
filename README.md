# NEXUS TERMINAL – Institutional-Grade Financial Suit

**NEXUS TERMINAL** is a full‑featured, Bloomberg‑style front‑end application that combines real‑time market data, advanced risk analytics (inspired by BlackRock Aladdin), global asset tracking (ADS‑B aircraft + AIS vessels), options flow, dark pool prints, macro heat maps, correlation analysis and a built‑in AI analyst. It is entirely client‑side and uses realistic mock data by default, with optional plug‑ins for live financial APIs

---

## Key Features

### Market & Order Book
- **Live watchlist** (equities, crypto, forex) with 1.5‑2.5s tick simulation
- **Interactive price charts** (1D/1W/1M/3M/1Y/5Y)
- **Level 2 order book** – 20‑level bid/ask ladder, depth chart (cumulative volume), time & sales tape
- **Portfolio tracker** – positions, P&L, allocation pie, sector exposure
- **Screener** – filter by asset class, signal (buy/hold/sell), price change

### Global Tracking (ADS‑B + AIS)
- **80 aircraft** – live positions, altitude, speed, squawk, registration, passenger count, airline, aircraft type (B777, A380, etc.)
- **120 vessels** – IMO/MMSI, flag, draft, beam, tonnage, cargo manifest, port ETA
- **Dedicated map views** for aircraft and vessels with category filters (cargo, tanker, container, bulk)
- **Alert system** – detects delayed flights, vessel distress signals, and compliance breaches

### Aladdin‑Style Risk Engine
- **VaR 99% / CVaR** – historical simulation with 20,000‑path Monte Carlo (GBM, jump‑diffusion, GARCH)
- **8 historical stress scenarios** – 2008 financial crisis, COVID‑19, 2022 rate hike, soft landing, etc.
- **12‑factor decomposition** – market beta, momentum, value, quality, interest rate, credit, FX, commodity, liquidity, idiosyncratic
- **Brinson attribution** – allocation, selection and interaction effects
- **48 compliance rules** – position / sector / country limits, cash minimum, leverage, ESG score, real‑time breach monitoring

### Advanced Market Modules
- **Options Chain** – strike, bid/ask, IV, delta, open interest, volume, max pain, put/call ratio
- **Dark Pool & Unusual Options** – sweep/block trades, bullish/bearish sentiment, premium heat map
- **Macro Heat Map** – GDP, CPI, policy rate, unemployment, debt/GDP for 20+ countries with regional averages
- **Correlation Lab** – dynamic correlation matrix, rolling 30D correlations, diversification score
- **Yield Curve** – US Treasury curve with 1Y / 2Y / pre‑2008 overlays, spread history
- **Commodities Dashboard** – energy, metals, agriculture with price charts
- **On‑Chain Analytics** – BTC MVRV, NVT, exchange flows, active addresses, miner revenue

### News & Macro
- **News feed** with impact rating (high/medium/low), ticker tagging, and full article view
- **Economic calendar** – upcoming releases (CPI, NFP, GDP, central bank meetings)
- **Earnings calendar** – actual vs estimate, surprise %, stock reaction
- **Central banks** – policy rates, meeting minutes, forward guidance for Fed, ECB, BoE, BoJ, PBOC

### Additional Intelligence Modules
- Short interest monitor (squeeze score, days‑to‑cover, borrow rate)
- Insider transactions (SEC Form 4) – buys/sells by CEO/CFO/directors
- Fear & Greed Index, VIX term structure, put/call ratio history
- Social sentiment – Reddit/Twitter mentions, bullish/bearish signals, price divergences
- Sector rotation – ETF performance across 11 sectors, economic cycle clock
- Fixed income screener (IG/HY, municipal bonds, yield to maturity, duration)
- Portfolio backtester, options P&L calculator, tax lot tracker (FIFO/LIFO/HIFO)

### AI Analyst Overlay
- Press `AI ANALYST` in the top‑right corner to open a chat window
- The AI has full context of your portfolio, risk metrics, factor exposures, stress tests, compliance breaches and tracking data
- Powered by **Claude Sonnet** (Anthropic) – requires an API key for production use (mock responses in demo)

### Live Alert System
- Custom price alerts (above/below threshold)
- Compliance breach alerts, market volatility alerts, tracking alerts
- Persistent panel with real‑time notifications

---

## Technology Stack

| Area | Libraries / Tools |
|------|-------------------|
| **Core** | HTML5, CSS3 (CSS Grid, Flexbox), ES6+ JavaScript |
| **Charts** | [Chart.js 4.4.1](https://www.chartjs.org/) |
| **Maps** | [Leaflet 1.9.4](https://leafletjs.com/) + OpenStreetMap tiles |
| **Fonts** | Google Fonts – Share Tech Mono, Orbitron |
| **Icons** | UTF‑8 symbols / emojis (lightweight, no external icon packs) |
| **Data** | Client‑side mock data generators (realistic ticker simulation, ocean lane interpolation, flight paths) |
| **External APIs** *(optional)* | Polygon.io (stocks/options), Alpha Vantage (macro/FX), FRED (economic data), CoinGecko (crypto) |

---

## 🛠️ How to Run

1. **Download** the `index.html` file (or copy the complete code into a local `.html` file).
2. **Serve it locally** – because Leaflet tiles and optional API calls require a web server context:
   ```bash
   # Using Python 3
   python -m http.server 8000
   # or use VS Code Live Server extension
   ```
3. **Open** `http://localhost:8000` in your browser (Chrome / Firefox / Edge recommended).
4. **No build step** – everything runs out of the box.

 The application uses **mock data** by default. All market prices, aircraft/vessel positions, options chains, dark pool prints and risk metrics are procedurally generated for demonstration purposes.

---

## API Integration (Live Data)

To replace mock data with real market feeds, edit the `NEXUS_API` object near the top of the `<script>` section:

```javascript
const NEXUS_API = {
  polygon: 'DEMO',      // https://polygon.io/dashboard/signup (free tier available)
  alphavantage: 'DEMO', // https://www.alphavantage.co/support/#api-key (free tier)
  coingecko: '',        // no key required for CoinGecko
  fred: 'DEMO',         // https://fred.stlouisfed.org/docs/api/api_key.html
};
```

- **Polygon.io** – stocks, options, forex, crypto (used for order book, options chain, correlation lab)
- **Alpha Vantage** – macroeconomic indicators (CPI, GDP, unemployment, central bank rates)
- **FRED** – alternative for yield curve, credit spreads, reserves data
- **CoinGecko** – on‑chain metrics (MVRV, NVT, miner revenue)

> The application includes **fetch wrappers** with timeouts and fallback to mock data when keys are `'DEMO'` or missing. Replace the placeholders with your own keys to enable live data.

---

## Project Structure (Single File)

The entire application is contained in one **`index.html`** file, organised into:

- **CSS** – dark/light theme, responsive grid layouts, map legend, custom scrollbars
- **HTML** – top bar, master navigation, 40+ module containers (hidden by default, activated via JS)
- **JavaScript** – all logic:
  - Navigation (`switchModule`, `switchRiskTab`, `showDashPanel`)
  - Mock data generators (`genAircraft`, `genVessels`, `genOptionsChain`, `genDPFlow`)
  - Chart initialisation (Chart.js + destroy routines)
  - Leaflet map handlers (aircraft/vessels markers, ocean lane interpolation)
  - Risk engine functions (`runMonteCarlo`, `renderScenarioDetail`, `buildCompliance`)
  - Alert system & AI chat overlay
  - Portfolio editor (add/remove positions, recalculate P&L)

---

## Data & Mock Behaviour

- **Ticker simulation** – prices change every 2–3 seconds using a bounded random walk (daily moves simulated intraday).
- **Aircraft & vessels** – positions are updated every 4 seconds: aircraft move linearly along great‑circle routes; vessels advance along predefined shipping lanes (`OCEAN_LANES` array covering North Atlantic, South Atlantic, Mediterranean, Suez, Indian Ocean, Trans‑Pacific, West/East Africa, etc.).
- **Order book** – 20 levels of bids/asks are regenerated based on the current price with realistic spreads and size distributions.
- **Options chain** – strikes are generated around the current price; IV, delta, and OI are calculated using a simplified Black‑Scholes approximation.
- **Risk metrics** – VaR, CVaR, factor exposures, stress impacts and Monte Carlo simulations are computed on‑the‑fly using standard financial formulas (historical simulation, GBM, jump‑diffusion).

---

## Demo Credentials / No Login Required

The terminal runs **100% client‑side** without any backend or authentication. All features are available immediately – you can edit the portfolio, set price alerts, run stress tests, and explore every module.

---

## License

This project is released under the **MIT License**. You are free to use, modify, and distribute it for personal or commercial purposes, provided that the original copyright notice is included.

---

## Contributing

Contributions are welcome – feel free to submit issues or pull requests to:
- Add new data adapters for additional APIs (e.g., Binance, Yahoo Finance)
- Improve risk model accuracy (GARCH calibration, copula‑based VaR)
- Extend tracking module with real ADS-B/AIS feeds (using public APIs like OpenSky, VesselFinder)
- Enhance the AI analyst prompt with more portfolio context

---

## Acknowledgements

- **Chart.js** for lightweight, responsive charts
- **Leaflet** & **OpenStreetMap** for mapping infrastructure
- **Google Fonts** for the terminal‑inspired typefaces
- **Anthropic** for the Claude API (AI analyst chat)
- All data providers (Polygon.io, Alpha Vantage, FRED, CoinGecko) for their generous free tiers

---

## Disclaimer

This software is provided for **educational and demonstration purposes only**. It does not constitute financial advice, and you should not make real trading decisions based on the simulated data. Past performance does not guarantee future results. Use of any external API is subject to that service’s terms of use and rate limits.

---

**Built with ❤️ for quants, traders, and data enthusiasts.**  
*NEXUS – Your Window into Global Markets.*
