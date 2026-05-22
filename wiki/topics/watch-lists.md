# Watch Lists

**Type:** Topic
**Status:** mature
**Last updated:** 2026-05-22
**Sources:** [WATCH_LISTS_GUIDE]

## Summary

The Watch Lists feature lets users create named groups of stocks, automatically capture DCF results after each analysis, and review portfolio-level upside/downside via interactive bar charts and histograms. Results are stored persistently and can be exported to CSV. It transforms the single-company FCF tool into a multi-company portfolio screening platform.

## Key Facts / Claims

- Unlimited watch lists; each has a name, optional description, and creation date [WATCH_LISTS_GUIDE]
- **Auto-capture**: when a watch list is set as active and capture is enabled, every DCF analysis is automatically saved [WATCH_LISTS_GUIDE]
- **Two view modes**: "Latest Analysis Only" (one row per stock, most recent valuation) and "All Historical Data" (full history per stock) [WATCH_LISTS_GUIDE]
- Visualisations: green/red upside/downside bar charts; reference lines at −20%, −10%, 0%, +10%, +20%; hover details [WATCH_LISTS_GUIDE]
- Performance distribution histogram for portfolio-level view [WATCH_LISTS_GUIDE]
- Export options: current view (latest per stock), complete historical data, individual stock history, all as CSV [WATCH_LISTS_GUIDE]
- Navigation: Watch Lists tab → Manage Lists (create/delete) → Capture Settings (activate, enable capture) → View [WATCH_LISTS_GUIDE]

## Connections

- [[dcf-valuation]] — Watch lists auto-capture DCF results; upside is calculated vs DCF fair value
- [[streamlit-ui]] — Watch Lists is a dedicated tab in the Streamlit app

## Open Questions

- Can watch lists be shared between users in a multi-user deployment?
- Is there a way to add stocks manually without running a DCF analysis first?
- How to handle historical captures when DCF assumptions change over time?

## Raw Notes

Watch list data is stored in a local SQLite or file-based database (implementation detail not fully documented in source).
Historical data mode is useful for tracking how valuation estimates have evolved over months.
