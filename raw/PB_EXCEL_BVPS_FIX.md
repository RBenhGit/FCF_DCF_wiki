# Fix: P/B Analysis — "Unable to calculate book value per share" (Excel Mode)

## Status
**FIXED** — 2026-05-21

## Symptom
When running P/B valuation with Excel files as the data source, the analysis fails with:

```
❌ P/B Analysis Error: Unable to calculate book value per share
```

This occurs even when all three financial statement files (Income Statement, Balance Sheet, Cash Flow Statement) are present and correctly formatted.

---

## Root Causes

Three independent bugs combined to make BVPS calculation impossible in pure Excel mode.

### Bug 1 — Wrong label column in `_extract_shareholders_equity()`
**File:** `core/analysis/pb/pb_valuation.py` (line 1194, pre-fix)

The Excel-loaded DataFrame has this column layout:

```
Index: [None, None, '', 'FY-9', 'FY-8', ..., 'FY-1', 'FY']
                      ↑
              row labels live here (column index 2)
```

The fuzzy fallback was hardcoded to read `iloc[:, 0]`, which is the all-`None` first column, so the search found nothing.

### Bug 2 — "Other Common Equity Adj" matched before "Common Equity"
**File:** `core/analysis/pb/pb_valuation.py` (row-label pass, pre-fix)

After fixing the column index, the keyword `'common equity'` is a substring of the label `'Other Common Equity Adj'`. That row appears earlier in the sheet (row 37) than `'Common Equity'` (row 38), so the adjustment line (value: −3,347M) was returned instead of the real equity total (343,479M for MSFT FY).

### Bug 3 — `shares_outstanding` never set in Excel mode
**File:** `core/analysis/pb/pb_valuation.py` + `core/analysis/engines/financial_calculations.py`

`FinancialCalculator.shares_outstanding` is initialized to `None` and is only populated by `_fetch_market_data()`, which calls yfinance. In pure Excel mode that call does not happen.

The Income Statement contains "Weighted Average Diluted Shares Out." (e.g., MSFT FY2024 ≈ 7,445M), but neither `FinancialCalculator` nor `PBValuator` extracted it before falling through to the network call.

Even if Bug 1 and Bug 2 had been fixed individually, Bug 3 would still cause the final `equity / shares` calculation to abort.

---

## Files Changed

| File | Change |
|---|---|
| `core/analysis/pb/pb_valuation.py` | Fixed label-column detection; added sub-component exclusions; added `_extract_shares_from_income_statement()` helper |
| `core/data_processing/adapters/excel_adapter.py` | Added `weighted_avg_shares` field extraction in `load()` |

---

## Changes in Detail

### `core/analysis/pb/pb_valuation.py`

#### 1. Dynamic label-column detection in `_extract_shareholders_equity()`
Replaced the hardcoded `iloc[:, 0]` with a left-to-right scan that finds the first column containing non-empty string values:

```python
# Before
row_labels = balance_sheet_data.iloc[:, 0] if len(balance_sheet_data.columns) > 0 else pd.Series()

# After
label_col_idx = 0
for _i in range(min(4, len(balance_sheet_data.columns))):
    _col_vals = balance_sheet_data.iloc[:, _i]
    if any(v is not None and isinstance(v, str) and v.strip() for v in _col_vals):
        label_col_idx = _i
        break
row_labels = balance_sheet_data.iloc[:, label_col_idx] if len(balance_sheet_data.columns) > 0 else pd.Series()
```

#### 2. Sub-component row exclusions in `_extract_shareholders_equity()`
Added a pre-filter before both the exact keyword pass and the loose fuzzy pass to skip rows that are sub-components rather than totals:

```python
_equity_row_excludes = ('adj', 'adjustment', 'other ', 'preferred', 'minority', 'lease')

for idx, norm_label in label_series_norm.items():
    if any(excl in norm_label for excl in _equity_row_excludes):
        continue
    ...
```

This prevents labels like `'Other Common Equity Adj'`, `'Minority Interest, Total'`, and `'Capital Leases'` from matching before the main equity row.

#### 3. Two-pass row-label matching with string-numeric parsing
Replaced the old single fuzzy pass (which only handled native `int`/`float` cell values) with:
- **Pass 1** — exact `equity_keywords` list matched against normalised row labels, with string-to-float parsing for comma-formatted numbers (e.g. `'343,479.0'`).
- **Pass 2** — loose `'equity'`/`'worth'` indicators, only if Pass 1 found nothing.

#### 4. New `_extract_shares_from_income_statement()` method
Called before the network fallback in `_calculate_bvps_from_statements()`. Reads `income_ltm` or `income_fy` from `financial_calculator.financial_data`, finds the correct label column dynamically, and returns shares in actual units (millions × 1,000,000):

```python
shares_row_names = [
    "Weighted Average Diluted Shares Out.",
    "Weighted Average Basic Shares Out.",
    "Diluted Shares Outstanding",
    "Basic Shares Outstanding",
    "Shares Outstanding",
]
```

Updated call order in `_calculate_bvps_from_statements()`:

```
1. self.financial_calculator.shares_outstanding  (set by yfinance when available)
2. _extract_shares_from_income_statement()       ← NEW: reads Excel income statement
3. _get_market_data()                            (network call, last resort)
```

### `core/data_processing/adapters/excel_adapter.py`

Added `weighted_avg_shares` to the `FinancialStatement` record built by `ExcelDataAdapter.load()`, so the `ISourceLoader` pipeline also has share counts without requiring a market data call:

```python
weighted_avg_shares=_iv(
    "Weighted Average Diluted Shares Out.",
    "Weighted Average Basic Shares Out.",
    "Diluted Shares Outstanding",
    "Basic Shares Outstanding",
),
```

---

## Verification

Tested against MSFT Excel files (`data/MSFT/`):

| Metric | Before fix | After fix |
|---|---|---|
| `shareholders_equity` | None (column not found) | 343,479M ✓ |
| `shares_outstanding` | None (yfinance not called) | 7,445M ✓ |
| `book_value_per_share` | Error | $46.14 ✓ |

All 83 unit tests in `test_fcf_engine.py`, `test_dcf_valuation.py`, `test_ddm_valuation.py`, `test_pb_fix.py`, `test_excel_adapter.py`, `test_excel_extraction.py`, and `test_equity_detection.py` pass.

---

## Variable Coverage Map (Excel Mode)

### Variables fully available from Excel files

| Variable | Source file | Row label |
|---|---|---|
| `revenue` | Income Statement | "Revenue" |
| `operating_income` / `ebit` | Income Statement | "Operating Income" / "EBIT" |
| `net_income` | Income Statement | "Net Income to Stockholders" |
| `tax_expense` | Income Statement | "Income Tax Expense" |
| `weighted_avg_shares` | Income Statement | "Weighted Average Diluted Shares Out." |
| `depreciation_amortization` | Cash Flow | "Depreciation & Amortization (CF)" |
| `operating_cash_flow` | Cash Flow | "Cash from Operations" |
| `capital_expenditures` | Cash Flow | "Capital Expenditures" |
| `net_borrowing` | Cash Flow | "Long-Term Debt Issued" + "Long-Term Debt Repaid" |
| `dividends_paid` | Cash Flow | "Dividends Paid (Ex Special Dividends)" |
| `shareholders_equity` | Balance Sheet | "Common Equity" / "Total Equity" |
| `total_assets` | Balance Sheet | "Total Assets" |
| `current_assets` | Balance Sheet | "Total Current Assets" |
| `current_liabilities` | Balance Sheet | "Total Current Liabilities" |
| `long_term_debt` | Balance Sheet | "Long-term Debt" |
| `cash_and_equivalents` | Balance Sheet | "Cash And Equivalents" |

### Variables that require a live market data call

| Variable | Used by | Notes |
|---|---|---|
| `current_price` | P/B ratio, DCF upside %, DDM yield | Not in any financial statement |
| `shares_outstanding` | DCF per-share, P/B (fallback) | Excel Income Statement used first (see above); market API only if not found |

### Variables absent from the standard Excel template

| Variable | Used by | Notes |
|---|---|---|
| `dividend_per_share` | DDM | Derived from `dividends_paid` ÷ `shares_outstanding`; requires both to be present |
