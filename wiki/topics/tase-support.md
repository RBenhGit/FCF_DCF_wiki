# TASE Support (Tel Aviv Stock Exchange)

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [TASE_SUPPORT_DOCUMENTATION], [COMPREHENSIVE_USER_GUIDE]

## Summary

The system provides first-class support for Israeli stocks traded on the Tel Aviv Stock Exchange (TASE). Key complexity: stock prices are in Agorot (ILA, 1/100 of a Shekel) while financial statements are in millions of Shekels (ILS). The system auto-detects TASE stocks via the `.TA` ticker suffix or yfinance currency metadata, converts between units, and presents dual-format displays. All valuation methods (DCF, P/B, DDM) handle TASE stocks correctly.

## Key Facts / Claims

- TASE stock detection: ticker ends with `.TA` (e.g. `TEVA.TA`, `CHKP.TA`) or yfinance reports `ILS` as the currency [TASE_SUPPORT_DOCUMENTATION]
- Stock prices from yfinance are in **Agorot (ILA)**; financial statements are in **millions ILS** [TASE_SUPPORT_DOCUMENTATION]
- DCF per-share scaling for TASE: `equity_value_millions_ILS × 1,000,000 × 100 / shares_outstanding` (result in Agorot) [TASE_SUPPORT_DOCUMENTATION]
- Conversion utilities: `convert_agorot_to_shekel()` (÷ 100), `convert_shekel_to_agorot()` (× 100), `get_price_in_shekels()`, `get_price_in_agorot()` [TASE_SUPPORT_DOCUMENTATION]
- DCF results for TASE include extra fields: `is_tase_stock`, `currency`, `value_per_share_agorot`, `value_per_share_shekels`, `currency_note` [TASE_SUPPORT_DOCUMENTATION]
- Streamlit UI shows dual format: e.g. "1,500 ILA (≈ 15.00 ₪)" for current price and fair value [TASE_SUPPORT_DOCUMENTATION]
- Market selection in UI: "US Market" vs "TASE (Tel Aviv)"; affects ticker processing and currency handling [COMPREHENSIVE_USER_GUIDE]
- No additional API calls required for currency detection; detection is integrated into the standard market data fetch [TASE_SUPPORT_DOCUMENTATION]
- Test suite: `test_tase_support.py` covers currency conversions, detection, price handling, DCF integration [TASE_SUPPORT_DOCUMENTATION]

## Connections

- [[dcf-valuation]] — DCF per-share calculation applies special TASE scaling
- [[pb-valuation]] — P/B analysis works correctly with ILS-denominated statements
- [[ddm-valuation]] — DDM works for TASE dividend payers
- [[data-sources-api]] — yfinance is the primary source for TASE currency detection and Agorot prices
- [[financial-metrics-schema]] — Currency-aware fields documented for TASE stocks

## Open Questions

- Is real-time USD↔ILS exchange rate integration needed for cross-market portfolio comparison?
- How to handle TASE companies that report in USD (some multinationals list on TASE but report in USD)?
- Support for other exchanges with similar currency duality (e.g. Singapore cents vs SGD)?

## Raw Notes

Israeli TASE ticker symbols are historically numeric (5-digit numbers); the `.TA` suffix is the yfinance convention.
`FinancialCalculator` stores `is_tase_stock` as a boolean property; downstream code should always check this before applying USD-based scaling.
