# Financial Metrics Schema

**Type:** Reference
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [Financial_Metrics_Schema]

## Summary

A comprehensive cross-reference table mapping each financial metric to its source statement, calculation method, API field names across all four providers (Alpha Vantage, FMP, yfinance, Polygon), Excel column names, and the formula used. Covers all core statement metrics, calculated FCF variants, valuation model inputs, and market data fields.

## Key Facts / Claims

- **Operating Cash Flow**: Alpha Vantage `operatingCashflow`; FMP `operatingCashFlow`; yfinance `Total Cash From Operating Activities`; Polygon `net_cash_flow_from_operating_activities` [Financial_Metrics_Schema]
- **Capital Expenditures**: all sources; converted to absolute value; Excel label "Capital Expenditures" or "CapEx" [Financial_Metrics_Schema]
- **Net Income**: Alpha Vantage `netIncome`; FMP `netIncome`; yfinance `Net Income`; Polygon `net_income_loss` [Financial_Metrics_Schema]
- **EBIT**: Alpha Vantage `ebit`; FMP `operatingIncome`; yfinance `EBIT`; Polygon `operating_income_loss` [Financial_Metrics_Schema]
- **Shareholders Equity**: Alpha Vantage `totalStockholderEquity`; FMP `totalStockholdersEquity`; yfinance `Stockholders Equity`; Polygon `equity` [Financial_Metrics_Schema]
- **FCFF** (calculated): `EBIT(1−TaxRate) + D&A − ΔWC − CapEx` [Financial_Metrics_Schema]
- **FCFE** (calculated): `NetIncome + D&A − ΔWC − CapEx + NetDebtPayments` [Financial_Metrics_Schema]
- **LFCF** (calculated): `OperatingCashFlow − CapEx` [Financial_Metrics_Schema]
- **Working Capital**: `CurrentAssets − CurrentLiabilities`; change = current minus prior period [Financial_Metrics_Schema]
- **Book Value per Share**: `ShareholdersEquity / SharesOutstanding` [Financial_Metrics_Schema]
- **P/B Ratio**: `MarketPrice / BVPS` [Financial_Metrics_Schema]
- DDM inputs: Dividend per Share (yfinance primary), Dividend Growth Rate, Dividend Yield, Payout Ratio [Financial_Metrics_Schema]

## Connections

- [[fcf-analysis]] — FCFF, FCFE, LFCF formulas defined here
- [[dcf-valuation]] — Discount rate, terminal growth rate, FCF projections inputs
- [[ddm-valuation]] — Dividend metrics sourced and calculated from this schema
- [[pb-valuation]] — BVPS and P/B ratio definitions
- [[data-sources-api]] — API field names per provider documented in this schema
- [[excel-data-integration]] — Excel column name aliases documented in this schema

## Open Questions

- How to handle metrics where yfinance field names change across library versions?
- Is EBITDA needed as a direct input to any calculation, or only as a diagnostic metric?

## Raw Notes

The schema file (`docs/api/Financial_Metrics_Schema.md`) is the authoritative cross-reference for field name mapping across all data providers.
