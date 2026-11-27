# Final Checklist: Noodles Crypto Analytics

Author: Mobassira Nasin, Data Analyst Intern at MVP Studio, Hamilton, New Zealand

This list shows what is finished and what is not. A ticked box means the work is done and has been checked. An empty box means it is not done.

## 1. Task 9 documents

- [x] Architecture diagram: `docs/architecture-diagram.png`
- [x] Data dictionary: `docs/data-dictionary.xlsx` (9 sheets, 133 columns)
- [x] Technical runbook: `docs/technical-runbook.md`
- [x] User guide: `docs/user-guide.md`
- [x] Demo presentation: `docs/demo-presentation.pptx` (16 slides, Arial font)
- [x] Demo video (shared as a Canva link in the README)
- [x] README with pictures, key facts, how to run and known limits
- [x] Final checklist: `docs/final-checklist.md` (this file)

## 2. GitHub

- [x] Public repository: `Mobassira18/noodles-crypto-analytics`
- [x] Both notebooks uploaded with the correct names
- [x] `.gitignore` uploaded, so the private `.env` file is kept out
- [x] `.env.example` uploaded as a template without a real password
- [x] README links and pictures open correctly
- [x] Repository description written
- [ ] Final search for the real database password in both notebooks and their older versions

## 3. Data warehouse (MySQL `noodles_dw`)

- [x] Star schema built: DimCurrency (78 rows), DimDate (2,131), DimPlatform (2), FactSocialEngagement (2,682)
- [x] Six summary tables built
- [x] Four views built
- [x] Row counts checked in MySQL Workbench after each load
- [x] Rows with an invalid date removed, with bad records saved in a rejects folder
- [ ] Slowly changing dimensions (SCD Type 2)
- [ ] Log files written by the pipeline
- [ ] Duplicate check for Reddit post IDs

## 4. Power BI

- [x] Task 7 report: 4 pages (Executive Dashboard, Time Series Analysis, Platform Analysis, Currency Deep Dive)
- [x] Task 8 dashboard: 3 visible pages and 1 hidden tooltip page
- [x] 28 measures, 8 relationships, bookmarks, drill-through page, Top N slider and date slicer
- [ ] Reports published to Power BI Service
- [ ] Scheduled data refresh
- [ ] Three time measures: Year to Date, Last 30 Days and Month-over-Month Change %

## 5. Sharing

- [ ] LinkedIn post published
- [ ] Video link and email added to the "Thank you" slide

## 6. Known limits

- The reports are not published online in Power BI Service, so the data is updated by hand: run the notebooks, then click Refresh in Power BI Desktop.
- Twitter and Reddit use different score formulas.
- Twitter data covers about one month. Reddit data covers almost five years.
- The project has no price data. The results show social media attention only.
