# Technical Specification: htick

## 1. System Overview
The platform acts as a dual-source verification tool for Shariah compliance of stocks. It aggregates data from:
1.  **Primary Source (Internal Analysis):** Raw financial data (Balance Sheets, Income Statements) processed via custom algorithms.
2.  **Secondary Source (External Validation):** Scraped data from existing providers (e.g., Islamicly) for cross-referencing.

## 2. Phase 1: Data Acquisition (Financial Inputs)

To calculate AAOIFI compliance independently, the system must ingest the following raw data points for every target stock.

### Required Data Points
| Data Point | Purpose | Source |
| :--- | :--- | :--- |
| **Total Debt** | Numerator for Debt Ratio (Short + Long Term Debt). | Financial API (FMP/Yahoo) |
| **Cash & Equivalents** | Numerator for Liquidity Ratio (Cash + Short Term Inv + Interest-bearing securities). | Financial API |
| **Market Capitalization** | Denominator for Debt & Liquidity Ratios. *Note: AAOIFI standard prefers a trailing average (e.g., 12-month avg) to smooth volatility, but current Market Cap is acceptable for MVP.* | Financial API |
| **Total Revenue** | Denominator for Income Ratio. | Financial API |
| **Non-Permissible Income** | Numerator for Income Ratio. Requires granular segment data. | Financial API / Manual |

### Recommended APIs
* **Financial Modeling Prep (FMP):** Good structural data for Balance Sheets.
* **Yahoo Finance (`yfinance`):** Free, useful for Market Cap and basic validation.
* **Casablanca Stock Exchange (Official Site):** May require custom HTML scraping for accurate Moroccan market data if global APIs are delayed.

---

## 3. Phase 2: External Data Scraping (The Comparator)

This module scrapes the "Islamicly" API to establish a baseline for comparison.

### Scraper Requirements
* **Rate Limiting:** Must implement a delay (e.g., `sleep(2)`) between requests to avoid IP bans.
* **Header Management:** Must rotate `User-Agent` and manage the `token` lifecycle.

### API Endpoints Strategy

#### A. Fetch Stock List
**Endpoint:** `POST https://livetradeapi.islamicly.com/api/RIapi/ViewAllStocksNewWeb`
**Payload:** `{"ExCntry":"MAR", "NumberOfRows":10000, "PageNo":1}`
**Goal:** Extract the `Ticker` and `Company Name` for all Moroccan stocks.

#### B. Fetch Stock Details
**Endpoint:** `GET https://livetradeapi.islamicly.com/api/RIapi/GetStockDetails?Ticker={TICKER}`
**Goal:** Extract their calculated ratios and compliance status.

### Data Storage Schema (NoSQL/JSONB recommended)
```json
{
  "ticker": "IAM",
  "fetch_date": "2023-10-27",
  "external_data": {
    "provider": "Islamicly",
    "status": "Compliant",
    "debt_ratio": 12.5,
    "liquidity_ratio": 8.2,
    "impure_income_ratio": 1.0
  }
}

```

---

## 4. Phase 3: Offline Processing & Logic

The core logic engine processes the raw financial data to generate the "Internal Analysis."

### The AAOIFI Standard 21 Algorithm

The stock is **COMPLIANT** only if ALL three conditions are met:

1. **Debt Screening:**


2. **Liquidity Screening:**


3. **Income Screening:**



### The Comparison Logic

After calculation, the system runs a comparison job:

1. Match `Internal_Status` vs `External_Status`.
2. If different, flag as `DISCREPANCY`.
3. Calculate Delta for ratios: `Delta_Debt = |Internal_Debt_Ratio - External_Debt_Ratio|`.

---

## 5. Phase 4: Frontend & Visualization

### Dashboard Structure

* **Market View:** Table of all stocks in the target market (e.g., Morocco).
* Columns: Ticker, Name, Our Status, Their Status, Confidence Score.


* **Detail View:** Specific page for a single stock.

### Visualization Specs

* **Ratio Bars:** Visual progress bars for Debt and Liquidity.
* Green zone: 0-29%
* Red zone: >30%


* **Comparison Matrix:**
A side-by-side table highlighting the differences:

| Metric | Our Calculation | External Source |
| --- | --- | --- |
| **Debt %** | **28%** (Pass) | **31%** (Fail) |
| **Source Data** | Market Cap: $10B | Market Cap: Unknown |

---

## 6. Implementation Roadmap

1. **Setup DB:** PostgreSQL with `stocks`, `financials`, and `scraped_logs` tables.
2. **Build Scraper:** Python script to run the Curls provided and dump to DB.
3. **Connect Financial API:** Script to fetch Balance Sheet/Market Cap for the tickers found in Step 2.
4. **Analyzer Script:** Run the math defined in Phase 3.
5. **Web UI:** Next.js/React frontend to display the database records.