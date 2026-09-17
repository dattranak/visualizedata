# VisualizeData Component Guidelines

This file is a guideline for AI to generate visualization dashboards.
Read and understand the intent, then generate appropriate HTML/CSS/JS code.
Do not copy this file literally. Adapt to the actual data.

---

# Data Pipeline

Two output files per input:

1. <input-name>.data_export.py
   - Reads the input file
   - Writes window.VD_DATA = { records: [...], summary: {...} }
   - to <input-name>.data.js
   - Must be compatible with Python 2 and Python 3

2. <input-name>.visualization.html
   - Standalone dashboard
   - Loads <input-name>.data.js via script src
   - Works with file:// protocol, no web server needed

---

# Dashboard Purpose

The primary goal is to give users:
- A clear overall picture of large datasets at a glance
- Progress tracking across time and milestones
- Performance trends over time
- A way to navigate and filter large record sets efficiently

Always ask: "What does the user need to understand quickly from this data?"
Prioritize clarity and insight over decoration.

---

# Components

## header

Purpose: Orient the user immediately.

Show:
- Dashboard title (derived from data content or filename)
- Source filename
- Total record count
- Time range if timestamps exist (e.g. "Jan 1 - Mar 31, 2025")
- One-line description of what the data represents
- Last updated timestamp if available

Derive all values from window.VD_DATA.summary.

---

## summary

Purpose: Give the user the most important numbers at a glance.

Show as KPI cards:
- Total
- Pass (green)  -- OK, PASS, DONE, SUCCESS
- Fail (red)    -- NG, FAIL, ERROR, BAD
- NY (yellow)   -- NY, N/A, SKIP, PENDING
- Pass rate %
- Any other critical metric derived from the data

Include a donut chart comparing Pass / Fail / NY.
Use the donut only when there are 2-4 meaningful categories.
If there are many status types, prefer a horizontal bar chart instead.

---

## progressTracker

Purpose: Show how work is advancing toward completion over time.
This is the most important component for progress and planning.

Use when: data has timestamps, dates, or sequential milestones.

Show:
- Cumulative pass/completion count over time (line chart)
- Remaining work over time (area or line)
- Projected completion date if trend is consistent
- Milestone markers on the time axis if milestone data exists
- Velocity: average completions per day/week

Chart type: Line chart or stacked area chart.
X-axis: time (date or datetime).
Y-axis: count or cumulative count.

This component answers: "Are we on track? When will we finish?"

---

## trendChart

Purpose: Show how a metric changes over time.

Use when: numeric metrics exist alongside timestamps.

Examples:
- Pass rate % over time
- Error count per day
- Performance score over builds or runs
- Coverage % over time

Chart type: Line chart (single or multi-line).
Use multiple lines only when comparing a small number of meaningful groups (max 5).
Always sort by time ascending.
Highlight anomalies or significant drops if detectable.

This component answers: "Is this getting better or worse?"

---

## groupBreakdown

Purpose: Compare performance or counts across categories or groups.

Use when: data has a grouping dimension (module, team, component, test suite, region).

Show:
- Pass/Fail/NY count per group as a stacked horizontal bar chart
- Sort by total count descending or by fail count descending
- Show top N groups if there are many (default top 20)

Chart type: Horizontal stacked bar chart.
Use horizontal bars when there are many categories or long category names.

This component answers: "Which group has the most failures? Which is performing best?"

---

## distributionChart

Purpose: Understand the shape and spread of a numeric metric.

Use when: a numeric column exists (duration, score, size, latency, count).

Show:
- Histogram of value distribution
- Key statistics: min, max, mean, median, p95

Chart type: Histogram.
Use a box plot when comparing distributions across groups.

This component answers: "What is the typical value? Are there outliers?"

---

## dataTable

Purpose: Let users explore and filter the full dataset.

Show:
- All records, one row per entry
- Search box (filters all columns in real time)
- Filter buttons for status (All / Pass / Fail / NY)
- Sortable columns
- Row count (visible / total)
- Paginate if records exceed 500 rows

Badge colors for status cells:
- OK / PASS / SUCCESS / DONE -> green
- NG / FAIL / ERROR          -> red
- NY / SKIP / PENDING / N/A  -> yellow
- numeric values             -> plain, right-aligned

Truncate long text cells with a tooltip showing the full value.

---

# Layout Order

Adapt the layout to the data. Suggested order:

1. header
2. summary (KPI cards + donut or status bar)
3. progressTracker   -- if time/date data exists
4. trendChart        -- if time + numeric metric exists
5. groupBreakdown    -- if grouping dimension exists
6. distributionChart -- if numeric metric exists
7. dataTable         -- always include

If time data does not exist, skip progressTracker and trendChart.
If no grouping dimension exists, skip groupBreakdown.
Always include header, summary, and dataTable.

---

# Chart Selection Rules

Choose the chart that makes the intended insight easiest to understand.
Do not choose a chart because it looks impressive.

| Goal                              | Chart type                  |
|-----------------------------------|-----------------------------|
| Progress over time                | Line / cumulative area      |
| Trend of a metric over time       | Line chart                  |
| Compare categories                | Horizontal bar chart        |
| Part of a whole (few categories)  | Donut chart                 |
| Part of a whole (many categories) | Horizontal bar chart        |
| Distribution of numeric values    | Histogram                   |
| Compare distributions             | Box plot                    |
| Correlation between two numerics  | Scatter plot                |
| Composition across categories     | Stacked bar chart           |
| Hierarchy / tree structure        | Treemap                     |
| Flow between stages               | Sankey diagram              |
| Sequential process stages         | Funnel chart                |
| Matrix of two categorical dims    | Heatmap                     |
| KPI vs target                     | KPI card or bullet chart    |

Key rules:
- Many categories -> prefer bar over pie/donut
- Precise comparison -> prefer bar over pie/donut
- Change over time -> prefer line over pie/donut
- Relationship between numerics -> prefer scatter over bar
- Distribution -> prefer histogram over bar

---

# Time and Progress Handling

This is the most critical section for this dashboard system.

When timestamps or dates exist:
- Always parse and sort by time ascending
- Detect granularity automatically: minute, hour, day, week, month
- Show cumulative progress line (total completions over time)
- Show daily/weekly velocity as a bar chart below the cumulative line
- If milestones or target dates exist, mark them on the time axis
- If data is recent enough, project the trend forward to estimate completion

When no timestamp exists but a sequence or order column exists:
- Use sequence as the x-axis
- Show trend over sequence

When neither exists:
- Skip time-based charts
- Focus on groupBreakdown and distributionChart

---

# Style Guidelines

Overall feel: clean, professional, data-dense but readable.

Colors:
- Page background: #f0f2f5
- Card background: white
- Header background: #1a2340 (dark navy)
- Header text: white
- Pass / OK: #16a34a (green)
- Fail / NG: #dc2626 (red)
- NY / Pending: #d97706 (amber)
- Primary accent: #3b82f6 (blue)
- Secondary accent: #8b5cf6 (purple)
- Neutral text: #1a1a2e
- Muted text: #667085

Typography:
- Font: Arial or system sans-serif
- Dashboard title: 22-26px bold
- Section titles: 16px semibold
- Body: 14px
- KPI values: 32-40px bold

Layout:
- Max content width: 1440px, centered
- Card border-radius: 12px
- Card shadow: 0 2px 8px rgba(0,0,0,0.06)
- Consistent spacing: 24px between sections, 16px between cards
- Responsive: stack to single column on narrow screens

Charts:
- Use canvas-based charts (no external library unless data complexity requires it)
- Keep chart height between 200px and 400px
- Always label axes
- Always show a legend when there are multiple series
- Use tooltips on hover
- Animate on load only if it does not hurt performance

---

# Data Quality

Always check and report:
- Total records parsed vs total lines in input
- Records skipped or unparseable
- Missing values per column
- Date range of the data

Show a small data quality badge in the header or footer.
If more than 10% of records could not be parsed, show a visible warning.

---

# Restrictions

- ASCII characters only in all .md files
- Python scripts must be compatible with Python 2 and Python 3
- HTML must work with file:// protocol
- No external CDN or network dependencies
- No web server required
- Generate minimal, clean code
- Keep total HTML file size under 100KB when possible
- Do not add unnecessary features or decorations
- Do not create files other than the two specified output files