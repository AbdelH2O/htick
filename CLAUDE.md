# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

htick is an AAOIFI Shariah compliance visualization platform that:
1. Calculates stock compliance ratios from raw financial data
2. Scrapes external providers (Islamicly) for comparison
3. Flags discrepancies between internal analysis and external sources

**Current Status:** Specification/planning stage - no implementation code yet.

## AAOIFI Standard 21 Compliance Rules

A stock is **compliant** only if ALL three conditions pass:

| Ratio | Formula | Threshold |
|-------|---------|-----------|
| Debt | Total Interest-Bearing Debt / Market Cap | < 30% |
| Liquidity | (Cash + Interest-Bearing Securities) / Market Cap | < 30% |
| Impure Income | Non-Permissible Income / Total Revenue | < 5% |

## Architecture (4 Phases)

1. **Data Acquisition** - Fetch financial data (Balance Sheets, Income Statements, Market Cap) from FMP/Yahoo Finance APIs
2. **External Scraping** - Scrape Islamicly API for comparative data with rate limiting
3. **Offline Processing** - Calculate ratios, compare against external data, flag discrepancies
4. **Frontend** - Next.js/React dashboard with market view and detail views

## External APIs

**Financial Data:**
- Financial Modeling Prep (FMP) - Balance sheets
- Yahoo Finance (yfinance) - Market cap, validation
- Casablanca Stock Exchange - Moroccan market data (may need HTML scraping)

**Islamicly API:**
- Stock list: `POST https://livetradeapi.islamicly.com/api/RIapi/ViewAllStocksNewWeb`
- Stock details: `GET https://livetradeapi.islamicly.com/api/RIapi/GetStockDetails?Ticker={TICKER}`
- Requires rate limiting (2s delays) and User-Agent rotation

## Database Schema

PostgreSQL with three tables:
- `stocks` - ticker, company name
- `financials` - debt, cash, revenue, market cap
- `scraped_logs` - external provider data (JSONB)

## Environment Variables

```
FINANCIAL_API_KEY=...
ISLAMICLY_TOKEN=...
DB_URL=postgres://user:pass@localhost:5432/htick
```

## Tech Stack

- Runtime: Node.js v18+ or Python 3.9+
- Frontend: Next.js/React
- Database: PostgreSQL
- Initial market focus: Morocco (MAR), designed for global extensibility
