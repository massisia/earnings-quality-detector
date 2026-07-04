# S&P 500 Earnings Quality Detector

An interactive dashboard that analyzes earnings quality across 498 S&P 500 companies using cash flow signals — with an AI-powered natural language search interface built on top.

**Live Dashboard:** https://massisia.github.io/earnings-quality-detector/earnings_quality_dashboard.html

---

## What It Does

Most investors focus on reported EPS without checking whether those profits actually converted to cash. This tool surfaces companies where earnings and cash flow diverge — which historically precedes restatements, dividend cuts, and underperformance.

Two signals drive the scoring:

**Cash Conversion Ratio (CCR)** — Operating Cash Flow divided by Net Income. Measures how much of every reported dollar of profit actually became cash. Above 1.0 is strong; well below 1.0 is a red flag.

**Accrual Ratio** — (Net Income − Operating CF − Investing CF) / Total Assets. Measures how much of earnings are driven by accounting entries rather than cash. Lower is better.

Both signals are normalized using Min-Max scaling and averaged into a single Earnings Quality Score (0–1).

---

## Key Design Decisions

**Loss-making companies are not excluded.** Most existing tools simply omit companies with negative net income because CCR becomes unreliable. Instead, this tool evaluates them separately on three cash health metrics:
- OCF / Cash Ratio — how fast cash is being consumed relative to reserves
- Cash Position — total liquidity available
- Cash Flow Trend — dual-horizon signal capturing both year-over-year and 3-year direction simultaneously

**AI commentary uses Karpathy's LLM Council methodology.** For the top 10 companies, five analyst personas (Bull, Bear, Forensic Accountant, Momentum Trader, Credit Analyst) each evaluate the company independently before a synthesis is produced. This reduces sycophancy and surfaces genuine tensions in the data. Click on one of the top 10 companies in the full rankings tab to view.

**Natural language search uses real-time web search.** The AI search feature queries the web for current information rather than relying solely on the static dataset, enabling it to contextualize findings with recent earnings news and analyst commentary.

---

## Dashboard Features

**Overview Tab**
- Top 15 and Bottom 15 leaderboards with color-coded scores
- Interactive sector strip plot — hover to identify companies, click to jump to Full Rankings
- Sector averages sorted by earnings quality

**Full Rankings Tab**
- Search by company name or ticker
- Filter by GICS sector
- Sort by sector or earnings quality score
- Column visibility toggles
- Click any top 10 company row to expand AI-generated analyst commentary

**Negative Net Income: Cash Analysis Tab**
- 27 companies evaluated on cash trajectory metrics
- Dual-horizon cash flow trend labels (Consistently Improving / Recently Improving / Recently Deteriorating / Consistently Deteriorating)

**AI Search (header — all tabs)**
- Natural language interface powered by Claude with real-time web search
- Secure backend via Vercel serverless function — API key never exposed to browser
- Returns analyst-style response and insight with specific numbers and benchmarks
- Filters the Full Rankings table based on query

---

## Tech Stack

| Layer | Tools |
|---|---|
| Data pipeline | Python, pandas, yfinance, scikit-learn |
| AI commentary | Anthropic Claude API (claude-sonnet-4-6) |
| Frontend | HTML, CSS, JavaScript |
| Backend API | Node.js, Vercel serverless functions |
| Hosting | GitHub Pages (dashboard), Vercel (API) |

---

## Data Sources

- **S&P 500 constituents** — GitHub datasets (public CSV)
- **Financial statements** — Yahoo Finance via yfinance API
- **AI commentary** — Anthropic Claude API with real-time web search

Of the 503 companies in the S&P 500, 498 returned sufficient financial data. 5 were excluded due to incomplete yfinance coverage. Of the 498, 469 reported positive net income and received a full CCR + Accrual score. The remaining 27 are evaluated separately on cash health metrics.

---

## Limitations & Planned Enhancements

**Current limitations:**
- Scores reflect a single year of data — a production version would track quarterly trends to identify deterioration before it becomes public
- AI commentary generated for top 10 companies only — full coverage would require a paid SEC filing API or larger API budget
- Natural language search works best for sector and metric queries; highly specific company questions depend on web search coverage

**Planned enhancements:**
- Quarterly automated refresh via GitHub Actions triggered at the start of each earnings season
- Credit Stress Early Warning System — a companion model that monitors financial health indicators and flags deteriorating credit quality before public disclosure
- MD&A integration — cross-referencing management narrative from SEC 10-K filings against quantitative signals to detect when language diverges from cash reality

---

## Repository Structure

- `earnings_quality_detector.ipynb` — Data pipeline, scoring, AI commentary, dashboard generation
- `earnings_quality_dashboard.html` — Generated interactive dashboard  
- `earnings_quality_data.json` — Scored company data (469 profitable companies)
- `health_monitor_data.json` — Cash health data (27 loss-making companies)
- `commentaries.json` — AI council commentary for top 10 companies
- `sp500_raw_data.csv` — Cached raw yfinance data
- `.gitignore`

**AI Search Backend:** https://github.com/massisia/earnings-quality-api

---

*Built by Massi — July 2026*
