# Streamlit Web Interface

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [COMPREHENSIVE_USER_GUIDE], [WATCH_LISTS_GUIDE]

## Summary

The primary user-facing interface is a Streamlit web application (`fcf_analysis_streamlit.py`) providing market selection, company loading, FCF analysis, DCF valuation, P/B analysis, DDM, and watch list management — all with interactive Plotly charts. It supports both Excel-backed and pure API (ticker) modes and exports PDF reports, CSVs, and chart images.

## Key Facts / Claims

- Launch: `python run_streamlit_app.py` or `streamlit run fcf_analysis_streamlit.py` or `run_fcf_streamlit.bat` [COMPREHENSIVE_USER_GUIDE]
- **Tabs**: FCF Analysis, DCF Valuation (with sensitivity matrix), P/B Analysis, DDM, Watch Lists [COMPREHENSIVE_USER_GUIDE]
- **Two modes**: Excel mode (select company folder) and Ticker mode (enter ticker symbol for API-only analysis) [COMPREHENSIVE_USER_GUIDE]
- Market selector: "US Market" vs "TASE (Tel Aviv)" — affects ticker processing and currency display [COMPREHENSIVE_USER_GUIDE]
- Real-time parameter adjustments: discount rate, growth assumptions sliders update DCF results live [COMPREHENSIVE_USER_GUIDE]
- PDF report generation via ReportLab: executive summary, FCF trends, DCF waterfall, sensitivity analysis, data quality report [COMPREHENSIVE_USER_GUIDE]
- Chart exports: PNG/SVG downloads for individual Plotly charts [COMPREHENSIVE_USER_GUIDE]
- Watch list tab: create lists, enable auto-capture of DCF results, view upside/downside bar charts, export to CSV [WATCH_LISTS_GUIDE]
- TASE stocks: dual Agorot/Shekel display with ₪ symbol and conversion tooltips [COMPREHENSIVE_USER_GUIDE]
- Ticker mode now continues to all analysis tabs (bug fixed — was stopping after price/shares display) [API_CONFIGURATION_GUIDE]

## Connections

- [[fcf-analysis]] — FCF Analysis tab displays FCFF, FCFE, LFCF with trend charts
- [[dcf-valuation]] — DCF tab exposes projection assumptions and sensitivity matrix
- [[pb-valuation]] — P/B tab shows historical P/B analysis and fair value scenarios
- [[ddm-valuation]] — DDM tab for dividend-paying companies
- [[watch-lists]] — Watch Lists tab for portfolio tracking
- [[tase-support]] — TASE currency display logic in Streamlit
- [[architecture]] — Streamlit app is the top-level application layer

## Open Questions

- How to deploy the Streamlit app as a multi-user service with session isolation?
- What is the best caching strategy to avoid re-running expensive API calls between page interactions?
- Can the app support simultaneous comparison of two companies side-by-side?

## Raw Notes

`run_streamlit_app.py` checks requirements before launch.
`presentation/streamlit_app_refactored.py` is a refactored entry point; presenter classes in `presentation/` layer separate display logic.
