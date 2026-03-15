## Overview
Currently, the NPA Petroleum Price Intelligence dashboard uses static/hardcoded reference prices and historical data. This issue proposes integrating real-time data feeds to enhance accuracy and provide users with live pricing information.

## Proposed Data Sources

### 1. **Bank of Ghana (BOG) FX Rates**
- **Current**: Fixed at 10.80 GHS/USD (as of Mar 13, 2026)
- **Target**: Live BOG interbank midpoint rates
- **API**: https://www.bog.gov.gh/treasury-and-the-markets/daily-interbank-fx-rates/
- **Frequency**: Daily updates or live streaming if available

### 2. **Brent Crude Oil Prices**
- **Current**: Manual scenario slider (hardcoded BENCH values)
- **Target**: Live ICE Futures Brent prices
- **Possible Sources**:
  - Alpha Vantage API (free tier available)
  - FRED (Federal Reserve Economic Data)
  - Platts/S&P Global feeds (premium)
  - Open APIs: IEX Cloud, Polygon.io

### 3. **COMAC Pricing Outlook**
- **Current**: Fixed COMAC Feb 1 2026 calibration
- **Target**: Weekly COMAC pricing outlook updates
- **Challenge**: COMAC may not have public API; consider web scraping or manual feed

## Technical Implementation

### Architecture
- Create a `data/` folder for API integration modules
- Implement a `FetchManager` class to handle rate limiting and caching
- Add a data refresh indicator in the UI (shows last update time)
- Implement error handling and fallback to hardcoded values if API fails

### Files to Create/Modify
```
src/
├── api/
│   ├── bog-fx-client.js      // BOG FX rate fetcher
│   ├── brent-price-client.js // Brent crude price fetcher
│   ├── comac-feed.js         // COMAC pricing parser
│   └── cache-manager.js      // Caching layer
├── utils/
│   └── data-updater.js       // Orchestrate all feeds
└── ghana_petroleum_v3.html   // Update to use new modules
```

### Backend Option (Optional)
- Create a lightweight Node.js/Python backend to:
  - Aggregate multiple data sources
  - Implement caching (Redis)
  - Rate-limit API calls to external services
  - Return data in optimized JSON format

## Acceptance Criteria
- [ ] BOG FX rates update at least daily automatically
- [ ] Brent crude prices reflect ICE Futures with <5 min latency
- [ ] UI displays "Last updated: HH:MM" timestamp for each feed
- [ ] Graceful degradation: if API fails, fall back to last cached value + warning
- [ ] No manual intervention required to refresh pricing data
- [ ] CORS issues resolved (use backend proxy or JSONP/alternatives)

## Related Components
- **Affected Calculations**: `getER()`, `calcGas()`, `calcDsl()`, `calcLPG()`, `calcATK()`
- **Affected UI Elements**: Top metric cards (Brent Crude, BOG FX Rate), forecast windows
- **Performance Impact**: Assess if real-time updates cause chart re-renders

## References
- NPA Guidelines: Section 4 (IPP Calculation)
- COMAC Feb 2026 Calibration baseline
- BOG Website: https://www.bog.gov.gh/

## Priority
High - Improves dashboard accuracy and user trust

## Labels
- enhancement
- real-time-data
- api-integration