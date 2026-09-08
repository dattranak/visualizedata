# VisualizeData

## Output
Two files per log:
1. <input-name>.data_export.py     -- Python script to generate the data JS file
2. <input-name>.visualization.html -- standalone dashboard HTML

## Data pipeline
- Python script reads the log and writes <input-name>.data.js
  containing: window.VD_DATA = { records: [...], summary: {...} };
- HTML loads <input-name>.data.js via a script src tag.
- Works with file:// -- no web server needed.
- User re-runs the Python script after each log update, then refreshes browser.

## Components to use

### header
Show: title, source filename, total record count, one-line description.
Derive all values from window.VD_DATA.summary.

### summary
Show: Total, Pass, Fail, NY as KPI cards + a donut chart (Pass=green, Fail=red, NY=yellow).
Map OK/PASS/DONE/SUCCESS -> Pass (green).
Map NG/FAIL/ERROR/BAD    -> Fail (red).
Map NY/N/A/SKIP/PENDING  -> NY   (yellow).

### dataTable
Show: all records, search box, row count, filter buttons
One row per test entry.
Badge colors:
  For pass/fail/ny type data:
    OK   -> green  (.badge-ok)
    NG   -> red    (.badge-fail)
    NY   -> yellow (.badge-ny)
    DONE -> indigo (.badge-done)
  For number:
    Choose suitable format

## Layout
header -> summary (KPI cards ->  group breakdown ) -> dataTable
Have a chart if data need it for more visualize from view

### Chart selection Rules

1. **Comparison -> Bar chart**

   * Use a bar chart when the primary goal is comparing the magnitude of categories.
   * Use a horizontal bar chart when there are many categories or when ranking is important.

2. **Time series -> Line chart**

   * Use a line chart when the x-axis represents time or another ordered sequence.
   * Use multiple lines only when comparing a small number of meaningful groups.

3. **Part-to-whole -> Pie / Donut**

   * Use pie or donut charts only when values represent parts of a meaningful total.
   * Prefer them when there are only a few categories.
   * Avoid them when there are many categories or when precise comparison is important.

4. **Composition across categories or time -> Stacked bar / Stacked area**

   * Use stacked bar charts for comparing composition across discrete categories.
   * Use stacked area charts when the composition changes continuously over time.

5. **Distribution -> Histogram / Box plot**

   * Use a histogram to understand the shape and frequency distribution.
   * Use a box plot to compare distributions using median, spread, and outliers.

6. **Relationship -> Scatter plot**

   * Use a scatter plot when both axes represent numeric variables and the goal is to identify correlation, clusters, or outliers.
   * Use bubble size to represent a third numeric variable when necessary.

7. **Matrix -> Heatmap**

   * Use a heatmap when two categorical dimensions form a matrix and color represents magnitude.

8. **Geography -> Map**

   * Use a map when geographic location is an important part of the analysis.
   * Do not use a map merely because geographic data exists if a bar chart communicates the comparison more clearly.

9. **Process -> Funnel**

   * Use a funnel when values represent sequential stages of a conversion or process.

10. **Flow -> Sankey**

    * Use a Sankey diagram when the main question concerns movement or flow between categories or stages.

11. **Hierarchy -> Treemap**

    * Use a treemap when data has hierarchical structure and the goal is to show relative contribution at multiple levels.

12. **KPI vs Target -> Bullet / KPI card**

    * Use a KPI card for a small number of important metrics.
    * Use a bullet chart when comparing actual performance against a target or benchmark.

### Important Principle

Do not choose a chart merely or pie/donut because it is visually attractive.

Choose the chart that makes the **intended comparison, trend, distribution, relationship, composition, or flow easiest to understand**.

When a chart type creates unnecessary visual complexity, prefer a simpler chart.

For example:

* Many categories -> prefer **bar** over pie/donut.
* Precise comparison -> prefer **bar** over pie/donut.
* Change over time -> prefer **line** over pie/donut.
* Relationship between numeric variables -> prefer **scatter** over bar.
* Distribution -> prefer **histogram/box plot** instead of bar.


## Style
Clean dashboard. Light gray background (#f0f2f5). Rounded corners (12px).
Dark navy header (#1a2340). Subtle card shadow.

## Restrictions
- Use ASCII characters only in all .md files.
- Python scripts must be compatible with both Python 2 and Python 3.