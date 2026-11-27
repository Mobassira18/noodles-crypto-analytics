# Technical Runbook: Noodles Crypto Analytics

**Purpose:** This runbook explains how to run, check and repair the Noodles Crypto Analytics pipeline. A person who did not build the pipeline should be able to follow it.

---

## 1. System Overview

The pipeline loads social media engagement data (Twitter and Reddit) for crypto tokens into a MySQL data warehouse called `noodles_dw`. Power BI reports read from the warehouse.

```
JSON / CSV files → Python + pandas (notebook 05) → MySQL noodles_dw (star schema)
                 → Python + SQL (notebook 06): summary tables and views → Power BI reports
```

| Part | Technology |
|------|------------|
| Source files | `v2_token.json`, `v4_x_tweets.json`, `v5_reddit_post_engagement.json`, `full_currency_analysis.csv` |
| Loading and cleaning | Python, pandas, SQLAlchemy, PyMySQL |
| Data warehouse | MySQL 8 (database `noodles_dw`) |
| Control | Two Jupyter notebooks (there are no separate script files) |
| Reports | Power BI Desktop (Import mode, MySQL connector) |

**The two notebooks**

| Notebook | What it does |
|----------|--------------|
| `05_data_warehouse_design.ipynb` | Creates the database and the star schema. Loads the dimensions and the fact table. Runs the first quality checks. |
| `06_powerbi_prep.ipynb` | Builds the summary tables and calculated columns. Creates the four Power BI views. Validates the results and writes the reject file. |

See `docs/architecture-diagram.png` for the picture and `docs/data-dictionary.xlsx` for every table and column.

---

## 2. Requirements

- **Windows** computer with **MySQL Server 8** running on `localhost`, port `3306`
- **Python 3** with these packages:

```
pip install pandas numpy sqlalchemy pymysql python-dotenv matplotlib seaborn
```

- A way to run notebooks: Jupyter Notebook, JupyterLab or VS Code
- **Power BI Desktop**, and **MySQL Connector/NET** on the same computer (Power BI needs it to read MySQL)
- A MySQL user with rights on `noodles_dw` (for example `root`)

### Database settings (`.env` file)

Notebook 06 reads the connection settings from a file named `.env` in the project folder. Notebook 05 has the settings inside its first cells.

```
DB_USER=root
DB_PASSWORD=<Kamal@2021>
DB_HOST=localhost
DB_PORT=3306
DB_NAME=noodles_dw
```

> **Security rule.** Never write the real password inside a notebook that is shared or uploaded. Keep the password only in `.env`, and add `.env` to `.gitignore` so that it is not uploaded to GitHub.

---

## 3. File Locations

| Resource | Path (inside the project folder) |
|----------|----------------------------------|
| Source JSON and CSV files | In the **same folder as the notebooks** (the notebooks read them by file name) |
| Notebooks | `05_data_warehouse_design.ipynb`, `06_powerbi_prep.ipynb` (project root) |
| Settings | `.env` (project root) |
| Reject files | `data/rejects/bad_records_<date_time>.csv` (created by notebook 06) |
| Summary export | `reports/powerbi_data_summary.csv` (created by notebook 06) |
| Schema export | `data_warehouse_schema.md` (created by notebook 05) |
| Power BI reports | `reports/NoodlesCrypto_TopPerformers.pbix`, `reports/NoodlesCrypto_ExecutiveDashboard.pbix` |
| Report guides | `reports/executive-dashboard-guide.md`, `reports/dax-measures-reference.md` |
| Documentation | `docs/` |

The folders `data/rejects/` and `reports/` must exist. Notebook 06 creates `data/rejects/` by itself. Create `reports/` before the first run.

---

## 4. Daily Operations

### 4.1 First build (empty database)

1. Start MySQL (see section 8.2 if it is not running).
2. Put the four source files in the same folder as the notebooks.
3. Open `05_data_warehouse_design.ipynb` and run the cells **from top to bottom**:

| Step | What the cell does | Check |
|------|--------------------|-------|
| Connect | Creates the database `noodles_dw` if it is missing and tests the connection | Message "MySQL connected successfully" |
| Create tables | Creates DimCurrency, DimDate, DimPlatform and FactSocialEngagement | A message for each table |
| Load DimCurrency | Reads `v2_token.json` | Shows the number of new symbols |
| Load enrichment | Reads `full_currency_analysis.csv` (skipped if the file is missing) | Message with the number of rows |
| Load DimDate | Creates one row for each day | Shows new dates inserted |
| Load DimPlatform | Inserts Twitter and Reddit | 2 platforms |
| Load Twitter facts | Reads `v4_x_tweets.json` and flattens `public_metrics` | Message "Twitter likes total" above 0 |
| Load Reddit facts | Reads `v5_reddit_post_engagement.json` | Number of rows loaded |
| Analysis cells | Show the top tokens, platform comparison and a time chart (optional) | Tables and charts appear |
| Data quality checks | Row counts, orphan check, date coverage | "No orphaned records found" |
| Export schema | Writes `data_warehouse_schema.md` | File created |

4. Open `06_powerbi_prep.ipynb` and run the cells in this order:

| Order | What the cell does |
|-------|--------------------|
| 1 | Installs the packages (skip it if they are already installed) |
| 2 | Creates the connection (`get_engine()` reads `.env`) |
| 3 | Defines the logger (`setup_logger()`) |
| 4 | Defines the functions for calculated columns |
| 5 | Defines the functions for the aggregation tables |
| 6 | **Runs the aggregation tables:** CurrencySummary, DailySocialSummary, SocialEngagementSummary |
| 7 | **Runs the calculated columns:** CurrencySummary_Enriched and FactSocialEngagementEnriched |
| 8 | **Creates the four views:** vw_ExecutiveDashboard, vw_TimeSeries, vw_SocialAnalytics, vw_PlatformDaily |
| 9 | Validates the tables and views (prints the row counts) |
| 10 | Checks the aggregation accuracy and writes `reports/powerbi_data_summary.csv` |
| 11 | Writes the reject file and prints the **data quality score** |

> On the **first** build, skip the cell that prints "first 5 rows" of the four views (the second code cell). The views do not exist yet, so this cell fails. It works on later runs.

**Typical run time:** [add the time you measure on your computer].

### 4.2 Refreshing the data (database already built)

1. Replace the source files with the new files, if there is new data.
2. Run the Twitter and Reddit load cells of notebook 05 again.
3. Run notebook 06 from the top, in the order above. The summary tables and the views are rebuilt each time.
4. Open Power BI Desktop and click **Home**, then **Refresh** (section 4.3).

**Safe to repeat.** The loads of DimCurrency, DimDate, DimPlatform and the Twitter facts skip rows that already exist (they compare the symbol, the date key, the platform name and the post id). The Reddit load has no post id check in the code. After you run it again, run the reject check in notebook 06 (step 11) and look for duplicate posts.

### 4.3 Power BI refresh

1. Open the `.pbix` file in Power BI Desktop.
2. Click **Home**, then **Refresh**.
3. Enter the MySQL user name and password if Power BI asks.
4. Wait until the message "Refreshing" disappears.
5. Press **Ctrl + S** to save.

The reports are not published to the Power BI Service. A scheduled refresh is therefore not set up.

### 4.4 Automatic runs (not set up)

The pipeline runs by hand. If automatic runs are needed later, notebook 06 can be started from the command line with:

```
jupyter nbconvert --to notebook --execute 06_powerbi_prep.ipynb
```

and this command can be set up as a daily task in the Windows **Task Scheduler**. This is a suggestion, and it has not been tested.

---

## 5. Monitoring and Validation

The pipeline writes its messages in the notebook output and in the console. It does not write log files.

### 5.1 Quick row count check

Run this in MySQL Workbench:

```sql
USE noodles_dw;
SELECT 'DimCurrency' AS table_name, COUNT(*) AS row_count FROM DimCurrency
UNION ALL SELECT 'DimDate', COUNT(*) FROM DimDate
UNION ALL SELECT 'DimPlatform', COUNT(*) FROM DimPlatform
UNION ALL SELECT 'FactSocialEngagement', COUNT(*) FROM FactSocialEngagement
UNION ALL SELECT 'vw_ExecutiveDashboard', COUNT(*) FROM vw_ExecutiveDashboard
UNION ALL SELECT 'vw_TimeSeries', COUNT(*) FROM vw_TimeSeries
UNION ALL SELECT 'vw_SocialAnalytics', COUNT(*) FROM vw_SocialAnalytics
UNION ALL SELECT 'vw_PlatformDaily', COUNT(*) FROM vw_PlatformDaily;
```

**Row counts at the last full build (November 2026)**

| Table or view | Rows |
|---------------|------|
| DimCurrency | 78 |
| DimDate | 2,131 |
| DimPlatform | 2 |
| FactSocialEngagement | 2,682 |
| CurrencySummary | 33 |
| DailySocialSummary | 619 |
| SocialEngagementSummary | 41 |
| CurrencySummary_Enriched | 33 |
| FactSocialEngagementEnriched | 2,682 |
| CurrencySocialEnrichment | 75 |
| vw_ExecutiveDashboard | 33 |
| vw_TimeSeries | 619 |
| vw_SocialAnalytics | 41 |
| vw_PlatformDaily | 628 |

New source data makes these numbers grow. A large drop, or a table with 0 rows, means that a step failed.

### 5.2 Twitter and platform check

```sql
SELECT dp.PlatformName,
       COUNT(*) AS fact_rows,
       SUM(f.Likes) AS total_likes,
       ROUND(AVG(f.EngagementScore), 4) AS avg_engagement_score,
       COUNT(DISTINCT dd.FullDate) AS distinct_post_days
FROM FactSocialEngagement f
JOIN DimPlatform dp ON f.PlatformKey = dp.PlatformKey
JOIN DimDate dd ON f.DateKey = dd.DateKey
GROUP BY dp.PlatformName;
```

**Pass:** Twitter has `total_likes` above 0, `avg_engagement_score` above 0, and 2 or more days.

### 5.3 Daily trend check

```sql
SELECT PlatformName,
       COUNT(DISTINCT FullDate) AS distinct_days,
       MIN(FullDate) AS min_date,
       MAX(FullDate) AS max_date
FROM vw_PlatformDaily
GROUP BY PlatformName;
```

**Pass:** each platform has 2 or more days.

### 5.4 Data quality checks in the notebooks

| Check | Where | Expected result |
|-------|-------|-----------------|
| Record counts of the four warehouse tables | Notebook 05, quality cell | Counts as in section 5.1 |
| Orphan facts (a fact without a currency) | Notebook 05 and 06 | "No orphaned records found" |
| Aggregation accuracy (currencies in the fact table and in CurrencySummary) | Notebook 06, step 10 | "Match" |
| Null values in key fields, duplicate posts | Notebook 06, step 11 | Reject file is empty |
| Data quality score | Notebook 06, step 11 | Printed as a percentage and a count of good rows |

**Reject file.** Bad rows are saved in `data/rejects/bad_records_<date_time>.csv` with a column `reject_reason` (for example `orphan_currency_fk`, `null_engagement_metric`, or a duplicate post). A file with only a header means that all checks passed.

---

## 6. Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| `ModuleNotFoundError: No module named 'pandas'` (or another module) | The package is not installed | Run the `pip install` command in section 2 |
| `OperationalError` or "Can't connect to MySQL server" | MySQL is not running, or the settings are wrong | Start MySQL (section 8.2). Check the user, password, host and port in `.env` and in the first cells of notebook 05 |
| `FileNotFoundError` for a JSON file | The source file is not in the folder of the notebook | Copy the file into the notebook folder |
| Notebook 06 fails on the "first 5 rows" cell | The views do not exist yet (first build) | Skip that cell, and run the cell that creates the views |
| `FileNotFoundError` when writing `reports/powerbi_data_summary.csv` | The folder `reports` does not exist | Create the folder `reports` |
| Warnings about table names in upper or lower case | MySQL on Windows stores table names in lowercase | Nothing to do. The function `mysql_table_name()` handles this |
| Fact table has rows but Twitter likes are 0 | `public_metrics` was not flattened | Run the Twitter load cell of notebook 05 again, then notebook 06, then refresh Power BI |
| Duplicate posts after a second Reddit load | The Reddit load has no post id check | Run the reject check (step 11 of notebook 06). Remove the duplicate rows, or rebuild the fact table |
| Power BI shows `MissingClientLibrary` | MySQL Connector/NET is missing | Install MySQL Connector/NET, restart Power BI Desktop |
| Power BI shows old numbers | The report was not refreshed | Click **Home**, then **Refresh** |
| A Power BI visual is empty | A slicer, a selected token or an old drill-through filter hides the data | Clear the filters (see `reports/executive-dashboard-guide.md`, section 8) |

---

## 7. Database Information

| Item | Value |
|------|-------|
| Server | `localhost` (port 3306) |
| Database | `noodles_dw` |
| Schema | One schema. Tables are dimensions, one fact table, aggregation tables and views (see `docs/data-dictionary.xlsx`) |
| Login | MySQL user from `.env` (for example `root`) |

---

## 8. Backup, Recovery and Service Control

### 8.1 Database backup and restore (MySQL)

Open **Command Prompt** and run (the path may differ on your computer):

```
"C:\Program Files\MySQL\MySQL Server 8.0\bin\mysqldump.exe" -u root -p noodles_dw > noodles_dw_backup.sql
```

To restore, create the database if it is missing and run:

```
"C:\Program Files\MySQL\MySQL Server 8.0\bin\mysql.exe" -u root -p noodles_dw < noodles_dw_backup.sql
```

Another way: in MySQL Workbench, click **Server**, then **Data Export**, choose `noodles_dw`, and export to a file.

The database can also be rebuilt from the source files. Run notebook 05 and then notebook 06 (section 4.1).

### 8.2 Starting MySQL

1. Press the **Windows key**, type `Services`, and open **Services**.
2. Find the line **MySQL80** (the name may differ).
3. Right-click it and click **Start**.

### 8.3 Code backup

The notebooks, the reports and the documents are kept in the project folder. They are versioned with Git and published in a GitHub repository (see `README.md`). Do not upload the `.env` file or any password.

---

## 9. Contacts

| Role | Name |
|------|------|
| Data engineer and report author | Mobassira Nasin |
| Database administrator | The same person (MySQL runs on the developer's computer) |
