# System Architecture

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [ARCHITECTURE_UNIFICATION], [COMPREHENSIVE_USER_GUIDE], [DEVELOPER_GUIDE]

## Summary

The application follows a strict two-phase pipeline: Phase 1 (input/normalisation) converts any data source into `List[FinancialStatement]` objects via the `ISourceLoader` protocol; Phase 2 (calculation/domain) runs the stateless FCFEngine and valuation models against those normalised records. `FinancialCalculator` is the only crossing point. This design eliminates the dual-path divergence bug discovered in May 2026.

## Key Facts / Claims

- **Phase 1** (input): ExcelDataAdapter, YFinanceAdapter, FMP, Polygon — all implement `ISourceLoader`; output is always `List[FinancialStatement]` with values in millions USD, missing fields as `None` [ARCHITECTURE_UNIFICATION]
- **Phase 2** (calculation): FCFEngine, DCFValuator, DDMValuator, PBValuator — consume `FinancialStatement` objects; never import Phase 1 [ARCHITECTURE_UNIFICATION]
- **Invariant**: Phase 1 never imports Phase 2; Phase 2 never imports Phase 1 [ARCHITECTURE_UNIFICATION]
- `ISourceLoader` protocol (runtime-checkable): `load(symbol, periods) → List[FinancialStatement]`, `supports_symbol(symbol) → bool`, `source_type` property [ARCHITECTURE_UNIFICATION]
- `FinancialStatement` dataclass extended in May 2026 with: `is_ltm`, `source_type`, `ebt`, `net_borrowing`, `units_millions`; `__post_init__` syncs `ebt ↔ pretax_income` [ARCHITECTURE_UNIFICATION]
- **Deprecated managers**: `CentralizedDataManager` and `EnhancedDataManager` both emit `DeprecationWarning` pointing to `MultiApiManager` [ARCHITECTURE_UNIFICATION]
- **Entry points**: `fcf_analysis_streamlit.py` (Streamlit web UI), `run_streamlit_app.py` (launcher), `CopyDataNew.py` (Excel extraction CLI) [COMPREHENSIVE_USER_GUIDE]
- Layer order: Application → Core Analysis → Data Processing → Data Sources [DEVELOPER_GUIDE]
- Module naming convention for valuation models: `{model_name}_valuation.py` [DEVELOPER_GUIDE]
- Before unification: 49 startup errors ("Cannot set unknown variable"), 114 log warnings; after fixes: 2 errors (test call bugs), 46 warnings [ARCHITECTURE_UNIFICATION]

## Connections

- [[fcf-engine]] — FCFEngine is the canonical Phase 2 calculation unit
- [[excel-data-integration]] — ExcelDataAdapter is the Phase 1 loader for Excel files
- [[data-sources-api]] — API adapters (yfinance, FMP, Polygon) are the Phase 1 loaders for live data
- [[fcf-analysis]] — FCF analysis is the primary output of Phase 2
- [[unified-data-system]] — VarInputData and FinancialVariableRegistry are part of the Phase 1 normalisation layer
- [[streamlit-ui]] — Streamlit app is the top-level application layer

## Open Questions

- When should `MultiApiManager` be replaced with a more formal source-routing strategy?
- How to add new data sources following the `ISourceLoader` protocol cleanly?
- What is the migration path for code that still imports the deprecated managers?

## Raw Notes

Root cause of the May 2026 diagnostic: `registry_config_loader` used a bare module import that silently failed, leaving VarInputData with an empty registry and causing every `set_variable` call to fail.
Fix: VarInputData now auto-calls `register_all_variables()` when the registry is empty.
