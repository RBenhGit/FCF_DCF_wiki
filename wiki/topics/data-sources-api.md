# API Data Sources

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [API_CONFIGURATION_GUIDE], [Financial_Metrics_Schema], [ARCHITECTURE_UNIFICATION], [UNIFIED_DATA_SYSTEM_GUIDE]

## Summary

The application integrates four external API data sources — yfinance (free, no key), Alpha Vantage, Financial Modeling Prep (FMP), and Polygon — through a priority-ordered fallback chain managed by `MultiApiManager`. Each source implements the `ISourceLoader` protocol; monetary values are normalised to millions USD before being passed to the calculation layer. API keys are stored in `.env` and configured via `data_sources_config.json`.

## Key Facts / Claims

- **Priority order**: yfinance (1, free) → Alpha Vantage/FMP (2, requires key) → Polygon (3, requires key) → Excel files (4, local fallback) [API_CONFIGURATION_GUIDE]
- **yfinance**: free, no key required; primary source for stock price, market cap, shares outstanding, dividend history [Financial_Metrics_Schema]
- **Alpha Vantage**: free tier 25 req/day, 5/min; good for basic financial data; configure in `data_sources_config.json` with `is_enabled: true` and API key [API_CONFIGURATION_GUIDE]
- **FMP (Financial Modeling Prep)**: free tier 250 req/day; best for comprehensive statements and ratios; recommended for production use [API_CONFIGURATION_GUIDE]
- **Polygon**: free tier 5 req/min; best for real-time market data [API_CONFIGURATION_GUIDE]
- `YFinanceAdapter` in `core/data_processing/adapters/yfinance_adapter.py` implements `ISourceLoader`; divides all monetary values by 1,000,000 to convert to millions; `net_borrowing = debt_issued + debt_repaid` [ARCHITECTURE_UNIFICATION]
- Adapters also exist for: Alpha Vantage (`alpha_vantage_adapter.py`), FMP (`fmp_adapter.py`), Polygon (`polygon_adapter.py`), Twelve Data (`twelve_data_adapter.py`) [UNIFIED_DATA_SYSTEM_GUIDE]
- **Deprecated**: `CentralizedDataManager` and `EnhancedDataManager` — use `MultiApiManager` instead [ARCHITECTURE_UNIFICATION]
- Rate limiting and circuit-breaker pattern: `core/data_processing/rate_limiting/enhanced_rate_limiter.py` [UNIFIED_DATA_SYSTEM_GUIDE]
- Multi-tier caching in `data/cache/`; `calculation_cache.py` caches calculation results; `background_refresh.py` refreshes asynchronously [UNIFIED_DATA_SYSTEM_GUIDE]

## Connections

- [[architecture]] — API adapters are Phase 1 ISourceLoader implementations
- [[fcf-engine]] — FCFEngine consumes normalised FinancialStatement objects from API adapters
- [[excel-data-integration]] — Excel is the alternative Phase 1 source; APIs are the fallback chain
- [[tase-support]] — yfinance is the primary source for TASE stock detection and ILS currency metadata
- [[financial-metrics-schema]] — Documents all field names per API provider for each metric
- [[real-time-prices]] — Real-time price service builds on top of the API layer

## Open Questions

- How to gracefully handle API quota exhaustion across multiple providers in a batch job?
- Is there a recommended strategy for caching API results to reduce costs in production?
- How to add Twelve Data as a full financial-statement source (currently partial)?

## Raw Notes

API keys go in `.env` for CLI usage; environment variables take precedence over config file values.
`configure_api_keys.py` script provides interactive key setup and source-testing.
