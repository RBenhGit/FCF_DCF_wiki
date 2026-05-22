# Real-Time Price Service

**Type:** Topic
**Status:** developing
**Last updated:** 2026-05-22
**Sources:** [REAL_TIME_PRICES_GUIDE], [Financial_Metrics_Schema]

## Summary

A dedicated real-time price service layer (`core/data_sources/real_time_price_service.py`) provides current stock prices independently of the full financial-statement data fetch. It is used to populate the DCF sensitivity matrix with live market prices and to display current vs fair-value comparisons in the Streamlit UI.

## Key Facts / Claims

- Module: `core/data_sources/real_time_price_service.py` [REAL_TIME_PRICES_GUIDE]
- Integration bridge: `core/data_sources/price_service_integration.py` [REAL_TIME_PRICES_GUIDE]
- Primary source: yfinance (free, no key); falls back to Alpha Vantage, FMP, Polygon [Financial_Metrics_Schema]
- For TASE stocks: price is in Agorot (ILA) from yfinance [TASE_SUPPORT_DOCUMENTATION]
- Used in: DCF upside/downside calculation, Watch List performance charts, current P/B ratio display

## Connections

- [[data-sources-api]] — Real-time price service is a specialised layer on top of the API infrastructure
- [[tase-support]] — TASE prices require Agorot handling
- [[dcf-valuation]] — Current market price is used for upside/downside calculation
- [[watch-lists]] — Real-time prices power the watch list performance bars

## Open Questions

- How frequently is the price refreshed during a Streamlit session?
- Is there a WebSocket/streaming price feed option for near-real-time updates?

## Raw Notes

Separate from the financial-statement fetch to allow lightweight price lookups without pulling full statements.
