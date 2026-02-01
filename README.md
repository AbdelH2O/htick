# htick

A platform to visualize and verify the AAOIFI Shariah compliance of stock market assets. This tool allows users to view compliance status based on independent financial analysis and compares these results against third-party providers (e.g., Islamicly) to identify discrepancies.

## 🚀 Features

- **Independent AAOIFI Analysis**: Calculates compliance ratios using raw financial data.
- **Visual Dashboards**: Displays Debt, Liquidity, and Impure Income ratios clearly.
- **Cross-Verification**: Scrapes and compares data from other halal screeners to validate accuracy.
- **Market Focus**: Initially targeted at the Moroccan Stock Exchange (MAR) with global extensibility.

## 📋 Methodology

This platform adheres to **AAOIFI Shariah Standard No. 21**. A stock is considered **Compliant** if it passes the *Sector Screen* (permissible core business) and meets the following *Financial Ratios*:

1.  **Debt Ratio**: `(Total Interest-Bearing Debt / Market Cap) < 30%`
2.  **Liquidity Ratio**: `(Cash + Interest-Bearing Securities / Market Cap) < 30%`
3.  **Impure Income**: `(Non-Permissible Income / Total Revenue) < 5%`

*> Note: Some variations (e.g., FTSE/MSCI) use Total Assets as the denominator or include Accounts Receivables screens (<49%). This platform defaults to the Market Cap method.*

## 🛠️ Getting Started

### Prerequisites
- **Node.js** (v18+) or **Python** (3.9+)
- **PostgreSQL** or similar database for offline processing
- **Financial Data API Key** (e.g., FMP, Yahoo Finance, or AlphaVantage)

### Installation

1.  Clone the repository:
    ```bash
    git clone https://github.com/yourusername/htick.git
    cd htick
    ```
2.  Install dependencies:
    ```bash
    npm install  # or pip install -r requirements.txt
    ```
3.  Configure environment variables:
    ```env
    FINANCIAL_API_KEY=your_key_here
    ISLAMICLY_TOKEN=your_token_here
    DB_URL=postgres://user:pass@localhost:5432/htick
    ```

## 🔄 Data Pipeline

The system operates in three stages:
1.  **Fetch**: Retreives raw financial statements (Income Statement, Balance Sheet) and Market Cap data.
2.  **Scrape**: Fetches comparative data from the Islamicly API for validation.
3.  **Process**: Normalizes data, calculates ratios, and flags discrepancies between our analysis and the third-party data.

## ⚠️ Disclaimer
This tool is for educational and informational purposes only. It does not constitute financial or religious advice. Screening criteria may vary between scholars.