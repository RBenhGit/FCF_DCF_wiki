# P/B Valuation (Price-to-Book)

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE], [PB_EXCEL_BVPS_FIX], [Financial_Metrics_Schema]

## Summary

The P/B (Price-to-Book) valuation module estimates intrinsic fair value by multiplying current book value per share against historical P/B percentile benchmarks derived from 15–20 quarters of the company's own trading history. It produces three scenarios (conservative, fair, optimistic) with confidence scores. A significant bug fix in May 2026 resolved "Unable to calculate book value per share" failures in pure Excel mode.

## Key Facts / Claims

- **Fair Value formula**: `Current BVPS × Historical P/B Percentile` [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- Three scenarios: **Conservative** = BVPS × Q25 P/B; **Fair** = BVPS × Median P/B; **Optimistic** = BVPS × Q75 P/B [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- Confidence metrics: data_completeness, trend_reliability, market_cycle_factor; score ≥ 0.8 is high confidence [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- **Book Value per Share** = Shareholders' Equity / Shares Outstanding [Financial_Metrics_Schema]
- **P/B Ratio** = Market Price per Share / BVPS [Financial_Metrics_Schema]
- Module: `core/analysis/pb/pb_valuation.py`; class `PBValuator` [DEVELOPER_GUIDE]
- **Bug (fixed 2026-05-21)**: Three independent bugs caused BVPS calculation to fail in pure Excel mode [PB_EXCEL_BVPS_FIX]
  - Bug 1: `_extract_shareholders_equity()` read label column at `iloc[:, 0]` (all-None) instead of scanning for the first non-empty string column
  - Bug 2: "Other Common Equity Adj" matched before "Common Equity" because it appeared earlier in the sheet
  - Bug 3: `shares_outstanding` was only populated by yfinance; pure Excel mode never set it; fix: extract "Weighted Average Diluted Shares Out" from Income Statement
- Fix location: `core/analysis/pb/pb_valuation.py` + `core/data_processing/adapters/excel_adapter.py` [PB_EXCEL_BVPS_FIX]
- May 2026 MSFT diagnostic: P/B tab produced 4 "all data sources failed" warnings in offline mode (expected — needs live price API) [ARCHITECTURE_UNIFICATION]

## Connections

- [[dcf-valuation]] — DCF and P/B serve as complementary valuation methods; compare outputs for cross-validation
- [[ddm-valuation]] — DDM is a third complementary method for dividend-paying companies
- [[excel-data-integration]] — Excel adapter fix (May 2026) is essential for P/B to work without API access
- [[data-sources-api]] — Live price required for current P/B ratio calculation; yfinance is primary source
- [[tase-support]] — P/B analysis works correctly for TASE stocks with proper ILS currency handling
- [[historical-pb-fair-value]] — Detailed methodology page for the historical-percentile approach

## Open Questions

- How to handle companies with negative book value (P/B model breaks down)?
- What is the minimum number of historical periods needed for statistically meaningful percentiles?
- Should industry P/B median be incorporated as a secondary benchmark alongside historical percentiles?

## Raw Notes

Data quality score > 0.8 = good; > 0.9 = excellent.
"Current vs fair" metric: positive = stock trading above fair value; within ±10% considered fairly valued.
