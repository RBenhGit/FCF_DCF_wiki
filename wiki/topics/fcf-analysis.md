# Free Cash Flow (FCF) Analysis

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [COMPREHENSIVE_USER_GUIDE], [Financial_Metrics_Schema], [ARCHITECTURE_UNIFICATION]

## Summary

The FCF Analysis module calculates three distinct Free Cash Flow variants — FCFF, FCFE, and LFCF — from company financial statements (Excel or API). It forms the primary input to DCF valuation and supports 1–10 year historical growth rate analysis with interactive Plotly visualizations. The canonical calculation logic lives in `FCFEngine`, which guarantees identical results regardless of whether the source is an Excel file or an API.

## Key Facts / Claims

- Three FCF types are supported: **FCFF** (Free Cash Flow to Firm), **FCFE** (Free Cash Flow to Equity), **LFCF** (Levered Free Cash Flow) [COMPREHENSIVE_USER_GUIDE]
- **FCFF formula**: `EBIT × (1 - TaxRate) + D&A - ΔWorkingCapital - CapEx`; tax capped at 35%, WC guard at 2× EBIT [ARCHITECTURE_UNIFICATION]
- **FCFE formula**: `NetIncome + D&A - ΔWorkingCapital - CapEx + NetBorrowing` [Financial_Metrics_Schema]
- **LFCF formula**: `OperatingCashFlow - CapEx` (simplest, directly from cash flow statement) [Financial_Metrics_Schema]
- Growth rates calculated for 1-year, 3-year, 5-year, and 10-year periods using annualised CAGR formula [COMPREHENSIVE_USER_GUIDE]
- Required metrics for FCFF: EBIT, Income Tax Expense, EBT, D&A; for FCFE: Net Income, D&A, Cash from Financing; for LFCF: Cash from Operations, CapEx [COMPREHENSIVE_USER_GUIDE]
- Validation bounds: tax rate 0–35%, growth rate −99% to 500%, discount rate 1–50% [COMPREHENSIVE_USER_GUIDE]
- The **FCFEngine** (`core/analysis/engines/fcf_engine.py`) is the single canonical calculator since the May 2026 unification; it is stateless and source-agnostic [ARCHITECTURE_UNIFICATION]
- `FinancialCalculator` is the only crossing point between the input layer (adapters) and the calculation layer (FCFEngine) [ARCHITECTURE_UNIFICATION]

## Connections

- [[dcf-valuation]] — FCFF is the primary input to DCF projections and terminal value calculation
- [[fcf-engine]] — FCFEngine is the canonical implementation of all three FCF formulas
- [[excel-data-integration]] — Excel adapter is one of two ISourceLoader implementations feeding FCFEngine
- [[data-sources-api]] — API adapters (yfinance, FMP, etc.) are the other ISourceLoader path
- [[architecture]] — Two-phase pipeline separates data loading from FCF calculation
- [[financial-metrics-schema]] — Detailed field-level mapping for all FCF components

## Open Questions

- What is the best strategy for normalising FCF across cyclical industries (e.g. oil)?
- How to handle negative FCFF or FCFE when used as a DCF base?
- Multi-currency FCF when a company reports in non-USD currency but trades on US exchanges?

## Raw Notes

Error codes relevant to FCF: CE001 (division by zero in growth), CE002 (invalid tax rate), CE003 (array length mismatch).
Single-company processing benchmarks: Excel load 1.2–2.1s, FCF calculation 0.1–0.2s, total 2.5–4.4s on standard hardware.
