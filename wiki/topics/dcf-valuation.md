# DCF Valuation

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [COMPREHENSIVE_USER_GUIDE], [Financial_Metrics_Schema], [ARCHITECTURE_UNIFICATION]

## Summary

The DCF (Discounted Cash Flow) valuation engine projects 10 years of Free Cash Flow, calculates a terminal value using the Gordon Growth Model, discounts everything back to present value, and derives a fair value per share after subtracting net debt. It supports sensitivity analysis across discount rates and terminal growth rates and produces a full upside/downside matrix relative to the current market price.

## Key Facts / Claims

- 10-year projection model with explicit cash flows in years 1–10 and a terminal value [COMPREHENSIVE_USER_GUIDE]
- **Years 1–5 growth**: defaults to 3-year historical CAGR (or user input) [COMPREHENSIVE_USER_GUIDE]
- **Years 5–10 growth**: defaults to 5-year historical CAGR (or user input) [COMPREHENSIVE_USER_GUIDE]
- **Terminal value formula (Gordon Growth Model)**: `TV = FCF₁₁ / (r - g)` where FCF₁₁ = FCF₁₀ × (1 + g) [COMPREHENSIVE_USER_GUIDE]
- Default assumptions: discount_rate=10%, terminal_growth=2.5%, yr1-5 growth=5%, yr5-10 growth=3% [COMPREHENSIVE_USER_GUIDE]
- **Enterprise Value** = Σ PV(FCFᵢ) + PV(TerminalValue); **Equity Value** = EV − NetDebt; **Fair Value/Share** = EquityValue × 1,000,000 / SharesOutstanding [COMPREHENSIVE_USER_GUIDE]
- Sensitivity matrix recalculates fair value across discount rates (typically 8%–14%) and terminal growth rates (1%–4%) [COMPREHENSIVE_USER_GUIDE]
- Key class: `DCFValuator` in `core/analysis/dcf/dcf_valuation.py` [DEVELOPER_GUIDE]
- `DCFValuator` takes a `FinancialCalculator` instance; exposes `calculate_dcf_projections(assumptions)` and `sensitivity_analysis(discount_rates, terminal_rates)` [COMPREHENSIVE_USER_GUIDE]
- Falls back to base FCF of $100M if no historical data is available [COMPREHENSIVE_USER_GUIDE]
- TASE stocks use a different per-share scaling: `equity_value_millions_ILS × 1,000,000 × 100 / shares_outstanding` (price in Agorot) [TASE_SUPPORT_DOCUMENTATION]

## Connections

- [[fcf-analysis]] — FCFF is the base FCF used in DCF projections
- [[ddm-valuation]] — Alternative income-based valuation model; complementary to DCF
- [[pb-valuation]] — Market-based valuation; used alongside DCF for cross-validation
- [[tase-support]] — TASE-specific currency scaling in per-share fair value output
- [[streamlit-ui]] — DCF tab exposes assumptions, projections, and sensitivity analysis to users
- [[watch-lists]] — DCF results are auto-captured to watch lists for portfolio tracking

## Open Questions

- How to incorporate Monte Carlo simulation for parameter uncertainty?
- Best approach for negative-FCF companies (start-ups, turnarounds)?
- Should the terminal value use an exit multiple method as an alternative to Gordon Growth?

## Raw Notes

Default `projection_years=5` in config but the full 10-year model is always computed.
The `DCFConfig` dataclass in `config.py` holds all default assumptions.
Validation error CE004: DCF assumption validation failure.
