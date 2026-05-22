# Historical P/B Fair Value Methodology

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]

## Summary

Instead of using generic industry P/B averages, this methodology derives fair value from the company's own historical P/B ratio distribution (15–20 quarters). Three fair-value scenarios are produced at the 25th, 50th, and 75th percentile of historical P/B, multiplied by the current Book Value per Share. Confidence scores quantify data completeness, trend reliability, and market-cycle position.

## Key Facts / Claims

- **Formula**: `Fair Value = Current BVPS × Historical P/B Percentile` [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- **Conservative scenario**: BVPS × Q25 P/B (challenging market conditions) [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- **Fair value scenario**: BVPS × Median P/B (statistically most robust, primary reference) [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- **Optimistic scenario**: BVPS × Q75 P/B (favourable market conditions) [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- Confidence score components: data_completeness (>0.8 good, >0.9 excellent), trend_reliability, market_cycle_factor [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- Overall confidence < 0.6 = "Very Low" — results not reliable [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- "Current vs fair" metric: positive = stock trading above fair value; within ±10% = fairly valued [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- Trend direction categories: "increasing", "decreasing", "stable"; trend strength 0–1 [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]
- `data_quality_score` field in `historical_analysis` dict: measures completeness of quarterly P/B history [HISTORICAL_PB_FAIR_VALUE_USER_GUIDE]

## Connections

- [[pb-valuation]] — Historical P/B fair value is the primary methodology within the P/B module
- [[data-sources-api]] — Historical quarterly prices and BVPS data sourced from API layer
- [[financial-metrics-schema]] — P/B Ratio and BVPS definitions

## Open Questions

- How many quarters are statistically sufficient for reliable percentile estimates?
- How to normalise historical P/B when a company undergoes significant capital structure changes?
- Should the market-cycle adjustment factor use a macro indicator (e.g. CAPE ratio)?

## Raw Notes

Developer examples are in `docs/examples/pb_historical_analysis_example.py` and `docs/examples/pb_statistical_analysis_example.py`.
