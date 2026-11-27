# User Guide: Noodles Crypto Dashboards

## 1. Overview

This guide explains how to open and use the two Noodles crypto dashboards. The dashboards show how much attention crypto tokens receive on social media (Twitter and Reddit). You do not need technical knowledge to use them.

The dashboards answer these questions:

- Which tokens receive the most attention?
- Is the attention of good quality?
- How does the attention change over time?
- Which platform, Twitter or Reddit, gives more attention to a token?
- How is one chosen token performing?

**What the data shows**

| Item | Value |
|------|-------|
| Posts counted | 2,682 (Reddit 1,538 and Twitter 1,144) |
| Tokens | 33 |
| Reddit period | 16 December 2020 to 16 November 2025 |
| Twitter period | 21 October 2025 to 18 November 2025 |
| Average engagement score | 25.99 |

Twitter covers only about one month, while Reddit covers almost five years. Please keep this in mind when you compare the two platforms over time.

---

## 2. The Two Dashboards

| Dashboard | File | Purpose |
|-----------|------|---------|
| Top Performers Report | `reports/NoodlesCrypto_TopPerformers.pbix` | Shows the leading tokens, trends, platforms and one token in detail |
| Executive Dashboard | `reports/NoodlesCrypto_ExecutiveDashboard.pbix` | A short overview for managers, with extra features such as saved views and pop-up details |

Both dashboards use the same data from the `noodles_dw` database.

---

## 3. How to Open a Dashboard

The dashboards are Power BI Desktop files. They have not been published online, so they are opened on a computer that has Power BI Desktop.

1. Find the file in the `reports` folder.
2. Double-click the file. Power BI Desktop opens it.
3. Wait until the pages are shown at the bottom of the window.
4. Click a page name at the bottom to change the page.

To load the newest data, click **Home** and then **Refresh**. A refresh needs the MySQL database to be running on the same computer. If the refresh fails, please ask the person who looks after the data (see Section 9).

---

## 4. Executive Dashboard

This dashboard has three pages. A fourth page, called Tooltip Platform, is hidden. It only appears as a small pop-up box.

### 4.1 Page 1: Executive Overview

**Purpose:** A quick overview for managers.

**What you see**

- **Four number boxes at the top:** Total Engagements (2,682), Avg Engagement Score (25.99), Active Tokens (33) and Total Posts.
- **Engagement Trend Over Time:** a line that shows the engagement on each day.
- **Top Tokens by Engagement:** a table of the leading tokens. The score column is coloured: red means a low score, and other colours mean a medium or high score.
- **Engagement Share by Platform:** a ring chart that shows the share of Twitter and Reddit.
- **Engagement Quality by Platform:** a bar chart that compares the average score of Twitter and Reddit.
- **Date range slicer and Top N Count slider:** these change what is shown (see Section 6).
- **Three buttons:** Overview, Top Engagement and Platform Comparison (see Section 6.6).

### 4.2 Page 2: Platform Performance Analysis

**Purpose:** To compare Twitter and Reddit over time.

**What you see**

- **Platform Engagement Trend:** one line for Reddit and one line for Twitter.
- **A table with one row for each platform:** total engagements, likes, retweets, average score and the time of the last data load.
- **A date range slicer.**

The column **LastEngagement** shows the time when the data was loaded into the database. It is not the date of a post. Please do not use it to judge trends.

### 4.3 Page 3: Token Drill-through

**Purpose:** To study one token in detail.

**What you see**

- **A token list:** click one token to select it.
- **Token Engagement Trend:** the score of that token over time.
- **Token Platform Split:** the share of Twitter and Reddit for that token.
- **Token Engagement Breakdown:** likes, comments, retweets and average score for each platform.
- **A back button** to return to the previous page.

The easiest way to reach this page is explained in Section 6.4.

---

## 5. Top Performers Report

This report has four pages.

| Page | What it shows |
|------|---------------|
| Executive Dashboard | A table of tokens, a bar chart of the top tokens, three number boxes, a date range slicer and a Top N Count slider |
| Time Series Analysis | Three charts that show engagement, number of posts, and likes, comments and retweets over time |
| Platform Analysis | A table and two charts that compare Twitter and Reddit |
| Currency Deep Dive | A token list with a trend chart and a platform chart for the chosen token |

The way to use the slicers, the slider and drill-through is the same as in the Executive Dashboard. See Section 6.

---

## 6. Common Tasks

### 6.1 Choose a date range

1. Find the date slicer. It shows two dates and a bar with two round handles.
2. Move the left handle to choose the start date. Move the right handle to choose the end date.
3. The charts that show time (the trend charts) update at once.

Please note:

- The date slicer is linked on all pages. A range chosen on one page also applies to the other pages.
- The totals for each token and each platform (the number boxes, the token table and the platform charts) come from data without dates. For this reason, the date range does not change them.
- If a chart becomes empty, the date range is probably too small. Move the handles outward to widen it.

### 6.2 Choose how many tokens to show (Top N Count)

1. Find the slider called **Top N Count** on the first page.
2. Move the round handle to the right to show more tokens, or to the left to show fewer. The range is 5 to 50.
3. The table or bar chart shows only that number of leading tokens.

### 6.3 Click to filter

1. Click a row in a table, or a bar or part of a chart.
2. The other visuals on the page now show only that item.
3. Click the same item again to remove the filter.

### 6.4 See one token in detail (drill-through)

Drill-through means "open a detail page for the item I choose".

1. Go to the first page.
2. Right-click the name of a token in the table.
3. Choose **Drill through**, then choose the detail page (**Token Drill-through** or **Currency Deep Dive**).
4. The detail page opens and shows only that token.
5. Click the back button (top left of the page) to return.

### 6.5 Choose a token from the list

On the detail page, you can also click a token in the list. The charts then show only that token. Click the same token again to show all tokens.

### 6.6 Use the saved views (Executive Dashboard only)

Three buttons at the top of the first page change the view.

| Button | What it shows |
|--------|---------------|
| Overview | All visuals. Top N is set to 10 |
| Top Engagement | The two platform charts are hidden. Top N is set to 5, so the table shows the five leading tokens |
| Platform Comparison | The line chart and the table are hidden, so the platform charts are larger. Top N is set to 10 |

In Power BI Desktop, hold the **Ctrl** key and click a button. A single click is not enough in Desktop.

### 6.7 See more details with the mouse (pop-up box)

On the first page of the Executive Dashboard, hold the mouse over the ring chart or over a bar of the platform chart. A small box shows the total engagements, the average score, and the share of likes and comments for that platform.

---

## 7. Understanding the Numbers

| Term | Meaning |
|------|---------|
| Total Engagements | The number of engagement records, which means posts about a token or on a platform. On the daily trend chart, it is the number of likes, comments and retweets added together for that day |
| Avg Engagement Score | The average score of the posts. A higher score means better quality of attention. In this data, scores run from about 1 to 111 |
| Likes | The number of likes. The total is 20,803 |
| Comments | The number of comments. The total is 12,145 |
| Retweets | The number of retweets. The total is 1,453. Only Twitter has retweets |
| Active Tokens | The number of different tokens in the data (33) |
| Token Engagement Rank | The position of a token. Rank 1 has the most engagements |
| Likes Share % and Comments Share % | The part of a platform's engagements that come from likes and from comments |

**How the score is worked out**

- A Twitter post: likes × 1.0, plus retweets × 2.0, plus comments × 1.5, plus quotes × 1.2, plus impressions × 0.01.
- A Reddit post: likes × 1.0, plus comments × 3.0.

Because the two platforms use different rules, the scores of Twitter and Reddit are not exactly equal in meaning. Please compare them with care.

**Examples from the data**

- The tokens with the most engagements are MIMUSD (288) and DAIUSD (277).
- Reddit gives about 57% of the posts and Twitter about 43%.
- The average score is about 21 for Reddit and about 27 for Twitter.

---

## 8. Good Practice

**Please do:**

- Refresh the data before an important meeting, and check the date of the latest data.
- Clear old filters if a chart looks empty.
- Remember that Twitter covers only one month.

**Please do not:**

- Use these numbers alone to decide on a purchase or sale of a token. The dashboards measure social media attention, not price.
- Judge trends with the column LastEngagement.
- Share the files outside the company without approval.

---

## 9. Questions and Problems

| Problem | What to try |
|---------|-------------|
| A chart is empty | Widen the date range, remove a selected token, or remove an old drill-through filter |
| The drill-through option is missing | Right-click the token name in the table, not in a number box |
| The Top N slider does not change the table | Check that you are on the page that has the slider, and move it again |
| A button does nothing | In Power BI Desktop, hold **Ctrl** and click |
| The refresh does not work | Check that MySQL is running. If it still fails, ask the data owner |

For other problems with the data or the dashboards, please contact the project owner, Mobassira Nasin.

---

## 10. Related Documents

| Document | Location |
|----------|----------|
| Power BI report guide (Top Performers) | `docs/powerbi-report-guide.md` |
| Executive dashboard guide | `reports/executive-dashboard-guide.md` |
| DAX measures reference | `reports/dax-measures-reference.md` |
| Technical runbook | `docs/technical-runbook.md` |
| Data dictionary | `docs/data-dictionary.xlsx` |
| Architecture diagram | `docs/architecture-diagram.png` |
