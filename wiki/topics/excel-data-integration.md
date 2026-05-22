# Excel Data Integration

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [COMPREHENSIVE_USER_GUIDE], [ARCHITECTURE_UNIFICATION], [PB_EXCEL_BVPS_FIX]

## Summary

Excel files exported from Investing.com are the primary data source for offline/verified analysis. Company data is organised in `data/companies/{TICKER}/FY/` (10-year history) and `/LTM/` (latest 12 months) folders containing three statement files. The `ExcelDataAdapter` implements `ISourceLoader`, reads these files, and emits `List[FinancialStatement]` with values in millions (native Excel unit). A critical P/B fix in May 2026 addressed BVPS calculation failures in pure Excel mode.

## Key Facts / Claims

- Folder structure: `data/companies/{TICKER}/FY/` and `/LTM/`; three files required: Income Statement, Balance Sheet, Cash Flow Statement [COMPREHENSIVE_USER_GUIDE]
- Auto-categorisation: files are matched by keywords "Income", "Balance", or "Cash" in the filename [COMPREHENSIVE_USER_GUIDE]
- **FY** = 10-year historical data; **LTM** = Latest Twelve Months (most recent 12-month window) [COMPREHENSIVE_USER_GUIDE]
- LTM replaces the most recent FY data point in all FCF calculations and DCF projections [COMPREHENSIVE_USER_GUIDE]
- Source format: Investing.com export with header row containing FY-9, FY-8 … FY columns; metric names in column index 2; values in millions with optional comma formatting [COMPREHENSIVE_USER_GUIDE]
- `ExcelDataAdapter` in `core/data_processing/adapters/excel_adapter.py` implements `ISourceLoader` [ARCHITECTURE_UNIFICATION]
- `ExcelDataAdapter.load()` added in May 2026 unification; now also extracts `weighted_avg_shares` field [PB_EXCEL_BVPS_FIX]
- Minimum 3 years required for meaningful growth trend analysis; 10 years optimal [COMPREHENSIVE_USER_GUIDE]
- File loading performance: 1.2–2.1s per company on standard hardware [COMPREHENSIVE_USER_GUIDE]
- Validation errors: DV001 (missing folder structure), DV002 (invalid format), DV003 (insufficient data), DV004 (metric extraction failure) [COMPREHENSIVE_USER_GUIDE]

## Connections

- [[fcf-engine]] — ExcelDataAdapter is a Phase 1 loader; FCFEngine is the Phase 2 consumer
- [[architecture]] — ExcelDataAdapter implements ISourceLoader in the two-phase pipeline
- [[pb-valuation]] — Excel BVPS bug fix is directly related to the P/B module
- [[data-sources-api]] — API sources are the fallback / alternative when Excel files are not available
- [[financial-metrics-schema]] — Lists Excel field names for each metric alongside API field names

## Open Questions

- How to handle multi-currency Excel files (e.g. a TASE company whose financial statements are exported in ILS)?
- What is the recommended migration path from Investing.com Excel exports to a pure API workflow?

## Raw Notes

`CopyDataNew.py` is the original CLI tool that populated DCF Excel templates from Investing.com exports.
The data/companies/ directory is empty by default; files must be placed there manually or exported from Investing.com.
