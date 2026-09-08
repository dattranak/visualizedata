# VisualizeData Workflow

## Trigger

When the user writes:

visualizedata <filename>

execute this workflow.

---

## Step 1: Read input

Search for the file in this order, exact the name of folder:
1. ./visualizedata/<filename>
2. ./input/<filename>
3. If not have ./input folder, file <filename> in ./ folder

---

## Step 2: Analyze data

Read and understand structure of input at Step1.2 or Step1.3
If have any specific request about data from user for any data, execute this also
If large data, consider to read it carefully to not burn much token for input
Some case:
  - If file is list of items, no any number: should summary it, the table display need consider to display detail data (if can) also
  - If there is some clasification items: should analyze and classify it
  - If input is large file, use some trick from your usable tool to analyze, no need to read full (But the python script need to execute for all data)
  - Strategy to make this input to visualiable data

Identify:
- total record count
- status or result column if present
- pass values: PASS, PASSED, SUCCESS, OK, TRUE, DONE, GOOD
- fail values: FAIL, FAILED, ERROR, NG, FALSE, BAD
- not-yet values: NY, N/A, SKIP, PENDING
- other relevant columns

Restriction:
- No output for flow or output for user need

Target:
- For you (agent) to understand data to easy proceed next step

---

## Step 3: Read components

Read ./visualizedata/components.md for component and style instructions.

---

## Step 4: Analyze input and generate Python data-export script

Create a Python script: <input-name>.data_export.py


The script must:
- Have header #!/usr/local/python-3.7.0/bin/python3.7
- Read the log file (same folder as the script, by filename only).
- Parse all records from the log.
- Write a JS file: <input-name>.data.js in the same folder.
- The JS file must assign one global variable:
    window.VD_DATA = { records: [...], summary: {...} };
- Each record object
- Summary object
- Print a success message when done.
- Use ASCII characters only in all strings and comments.
- Use Python syntax compatible with both Python 2 and Python 3.

The user runs this script once after each log update:
    python <input-name>.data_export.py
No server required. The HTML loads the .data.js via a script src tag.

---

## Step 5: Execute Python script to generate JS

You (agent) will run script python <input-name>.data_export.py that you just generate

---

## Step 6: Generate HTML

Create a single standalone HTML file with embedded CSS and JS.
Do not copy any template literally.
Write minimal clean code based on the actual data.
No comment and redundant spaces

### Data loading rule
- Load data via: <script src="<input-name>.data.js"></script>
- On page load, read window.VD_DATA (populated by the .data.js file).
- All KPI cards, charts, group breakdowns, and table rows must be rendered
  from window.VD_DATA -- never hardcode data in the HTML.
- Show a clear error message if window.VD_DATA is not found.
- This works when the HTML is opened directly as file:// in any browser.

### Table row rule
- One row per test entry (do NOT split rows for same test entry).

---

## Step 7: Save output

- For case not found ./input folder :
Create two files next to the log file:
1. <input-name>.data_export.py     -- the data export script
2. <input-name>.visualization.html -- the dashboard HTML
- For case found ./input folder :
Create two files in the ./output folder (make ./output directory if not have yet):
1. ./output/<input-name>.data_export.py     -- the data export script
2. ./output/<input-name>.visualization.html -- the dashboard HTML
3. ./output/<input-name>.data.js  -- the data exported from input by the data export script

---

## Step 8: Clean temporary files

---

## Workflow for user after log update

1. Run:  python <input-name>.data_export.py
2. Refresh (or re-open) the HTML file in any browser.
No agent re-run needed.

---

## Restrictions

- Do not create intermediate files other than the two outputs above.
- Use file editing to create the output directly.
- If files already exist, overwrite them.
- Use ASCII characters only in all .md files.
- Python scripts must be compatible with both Python 2 and Python 3.
- No chart draw with python