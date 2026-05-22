# FCFEngine

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [ARCHITECTURE_UNIFICATION]

## Summary

`FCFEngine` is the single canonical, stateless calculator for FCFF, FCFE, and LFCF, introduced in the May 2026 architecture unification. By separating the calculation logic from the data loading layer, it guarantees that identical numeric data produces byte-for-byte identical FCF results regardless of whether the source was an Excel file or an API — eliminating a long-standing dual-path bug.

## Key Facts / Claims

- File: `core/analysis/engines/fcf_engine.py` (new in May 2026) [ARCHITECTURE_UNIFICATION]
- Methods: `calculate_fcff(records)`, `calculate_fcfe(records)`, `calculate_lfcf(records)` — all accept `List[FinancialStatement]` and return `List[float]` in millions USD [ARCHITECTURE_UNIFICATION]
- Canonical constants: `TAX_CAP=0.35`, `DEFAULT_TAX=0.25`, `WC_GUARD=2.0` (working-capital guard: change capped at 2× EBIT) [ARCHITECTURE_UNIFICATION]
- Stateless and source-agnostic: no dependency on data-loading infrastructure [ARCHITECTURE_UNIFICATION]
- Tested by `tests/unit/test_fcf_engine.py` — 15 unit tests including a `TestSourceTypeParity` class that verifies Excel vs API parity [ARCHITECTURE_UNIFICATION]
- `FinancialCalculator` delegates to `FCFEngine` via `_build_records_from_metrics(metrics)` which converts the pre-computed metrics dict to `List[FinancialStatement]` [ARCHITECTURE_UNIFICATION]
- The old dual paths (legacy tax-cap-35% path and VarInputData tax-cap-50% path) that caused divergent results are superseded but methods kept for backwards compatibility [ARCHITECTURE_UNIFICATION]

## Connections

- [[fcf-analysis]] — FCFEngine is the calculation core behind the FCF analysis feature
- [[architecture]] — FCFEngine is Phase 2 of the strict two-phase pipeline
- [[excel-data-integration]] — ExcelDataAdapter implements ISourceLoader and produces List[FinancialStatement] consumed by FCFEngine
- [[data-sources-api]] — YFinanceAdapter and other API adapters also produce List[FinancialStatement] for FCFEngine

## Open Questions

- Should FCFEngine expose a batch method for multiple companies in one call?
- How to extend FCFEngine for non-standard FCF variants (e.g. FCFF with lease adjustments)?

## Raw Notes

Before the unification: `financial_calculations.py` was ~3,600 lines with the calculation duplicated twice. The FCFEngine distilled all of that into one authoritative class.
MSFT Excel vs API parity spot-check: residual differences of 3–8% are data-provider differences, not algorithm differences.
