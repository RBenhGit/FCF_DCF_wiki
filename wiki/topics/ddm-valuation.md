# DDM Valuation (Dividend Discount Model)

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [DDM_USER_GUIDE], [Financial_Metrics_Schema], [DEVELOPER_GUIDE]

## Summary

The DDM module values equity by discounting expected future dividends to their present value. The system supports five variants: Zero Growth, Gordon Growth (single-stage), Two-Stage, Multi-Stage (H-Model), and Variable Growth. It is best suited to dividend-paying companies with predictable payout patterns and is complementary to the DCF model.

## Key Facts / Claims

- Core equation: `P₀ = Σ(Dₜ / (1 + r)ᵗ)` — stock value equals PV of all future dividends [DDM_USER_GUIDE]
- **Zero Growth DDM**: `P₀ = D / r`; used for preferred stocks and stable utilities [DDM_USER_GUIDE]
- **Gordon Growth Model**: `P₀ = D₁ / (r - g)`; requires r > g; most widely used variant [DDM_USER_GUIDE]
- **Two-Stage DDM**: separates a high-growth phase (n years) from a terminal stable-growth phase; terminal value = `D₀(1+g₁)ⁿ(1+g₂) / (r−g₂)` [DDM_USER_GUIDE]
- **H-Model**: gradual linear transition from high (gₛ) to stable growth (gₗ); formula adds a correction term to Gordon Growth [DDM_USER_GUIDE]
- **Variable Growth DDM**: arbitrary growth rates per user-defined time windows, phases summed independently [DDM_USER_GUIDE]
- Required return `r` estimated via CAPM, dividend growth model, or bond yield + risk premium [DDM_USER_GUIDE]
- Sustainable growth rate: `g = ROE × Retention Ratio` [DDM_USER_GUIDE]
- Module: `core/analysis/ddm/ddm_valuation.py`; class `DDMValuator` [DEVELOPER_GUIDE]
- Known bug fixed in May 2026: `dividends[...].count()` returned a `pd.Series` (not a scalar); also `get_variable()` could return a string causing numeric comparison crash [ARCHITECTURE_UNIFICATION]
- Dividend data sourced primarily from yfinance; falls back to other API sources [Financial_Metrics_Schema]
- Cannot be directly applied to non-dividend-paying stocks; FCFE model is the recommended alternative [DDM_USER_GUIDE]

## Connections

- [[dcf-valuation]] — DCF is the primary valuation complement for non-dividend companies
- [[pb-valuation]] — P/B analysis provides a market-ratio cross-check alongside DDM
- [[data-sources-api]] — Dividend per share and dividend history sourced from yfinance and other APIs
- [[financial-metrics-schema]] — Dividend per Share, Dividend Growth Rate, Yield, Payout Ratio are the key DDM inputs
- [[architecture]] — DDMValuator is Phase 2 of the two-phase pipeline; takes normalised FinancialStatement objects

## Open Questions

- How to handle companies that recently initiated or suspended dividends mid-history?
- How to incorporate share buybacks as an implied dividend in the DDM framework?
- What is the right model to use when a company switches from buybacks to dividends?

## Raw Notes

Two-Stage Python implementation in DDM_USER_GUIDE includes a `two_stage_ddm()` reference function.
Validation: cross-check DDM results against DCF and comparable company multiples.
Sensitivity to small changes in `g` or `r` is the primary practical challenge.
