# Unified Data System

**Type:** Topic
**Status:** developing
**Last updated:** 2026-05-22
**Sources:** [UNIFIED_DATA_SYSTEM_GUIDE], [ARCHITECTURE_UNIFICATION]

## Summary

The Unified Data System is the data-management layer that integrates Excel and API sources through a `FinancialVariableRegistry` and `VarInputData` store. It provides standardised variable naming, type validation, unit conversion, source mapping, and multi-tier caching. A bootstrap bug (empty registry) was fixed in May 2026 so that `VarInputData` now auto-registers all standard variables on initialisation.

## Key Facts / Claims

- Key classes: `VarInputData` (`core/data_processing/var_input_data.py`), `FinancialVariableRegistry` (`core/data_processing/financial_variable_registry.py`) [UNIFIED_DATA_SYSTEM_GUIDE]
- `VarInputData.__init__` now auto-calls `register_all_variables()` when the registry is empty — fixes the "Cannot set unknown variable" errors from May 2026 [ARCHITECTURE_UNIFICATION]
- 7 missing variable definitions added in May 2026: `intrinsic_value`, `current_price`, `equity_value`, `discount_rate`, `dividend_growth_rate`, `gordon_growth_rate`, `current_pb_ratio` [ARCHITECTURE_UNIFICATION]
- Registry provides: standardised names, type checking, source-to-standard field mapping, unit conversion (thousands → millions) [UNIFIED_DATA_SYSTEM_GUIDE]
- **Data priority**: Excel (primary when available) → API fallback chain → multi-tier cache [UNIFIED_DATA_SYSTEM_GUIDE]
- `background_refresh.py`: async background refresh of stale cached data [UNIFIED_DATA_SYSTEM_GUIDE]
- `calculation_cache.py`: caches calculation results to avoid redundant computation [UNIFIED_DATA_SYSTEM_GUIDE]
- `registry_config.yaml`: configures variable registry settings [UNIFIED_DATA_SYSTEM_GUIDE]
- `data_contracts.py`: defines `FinancialStatement` and other canonical data structures [ARCHITECTURE_UNIFICATION]

## Connections

- [[architecture]] — The unified data system is the Phase 1 normalisation infrastructure
- [[data-sources-api]] — API adapters feed data into the unified system
- [[excel-data-integration]] — Excel adapter is the primary data source feeding the registry
- [[fcf-analysis]] — Normalised variables from the registry power FCF calculations

## Open Questions

- What is the recommended way to add a new financial variable to the registry?
- How does the cache handle stale data when a company restates financials?
- Is there a registry export/import for reproducible analysis?

## Raw Notes

`standard_financial_variables.py` defines the canonical set of variable schemas.
`variable_processor.py` handles the variable processing pipeline before variables reach `VarInputData`.
