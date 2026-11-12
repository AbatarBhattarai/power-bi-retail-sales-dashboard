# Build Your Power BI Retail Sales Dashboard — Step by Step

**Time needed:** about 30–45 minutes.
**What you end up with:** a one-page Power BI dashboard called **Retail Sales Dashboard**, built from your Retail Sales Analysis data (1,200 orders from 2024), a saved file named `retail-sales-dashboard.pbix`, and a screenshot you can put on GitHub and LinkedIn.
**What you need:** a Windows computer and the file `retail_orders_clean.csv` from this project folder. Everything is free.

Work through it top to bottom. Don't skip steps — later steps use things you make in earlier ones.

---

## Part 1 — Install Power BI Desktop (about 5 minutes)

**Step 1.** Open your browser and go to the Microsoft Store, search for **Power BI Desktop**, and install it. It is free.
- Alternative: download it from `https://www.microsoft.com/en-us/download/details.aspx?id=58494` (the official Microsoft download page).
- You should see: the Power BI Desktop app installing. If it asks you to sign in, you can click **Skip** / close that window — no account is needed to build and save a file on your own computer.

**Step 2.** Open Power BI Desktop.
- You should see: a start screen with "Get data" options and a blank report canvas. If a sign-in pop-up appears, close it with the X.

---

## Part 2 — Load the data (about 3 minutes)

**Step 3.** Click **Get data** (in the Home ribbon at the top, or the big "Get data" button on the start screen).
- A list appears. Click **Text/CSV**, then click **Connect** at the bottom right.

**Step 4.** Find and select **`retail_orders_clean.csv`** from this project folder, then click **Open**.
- You should see: a preview table with 1,200 rows and these columns: `order_id, order_date, region, channel, category, product, unit_price, quantity, discount, customer_id, revenue`.

**Step 5.** In the preview window, click **Transform Data** (not "Load").
- You should see: the **Power Query Editor** opens. This is where you check the data before it goes into the dashboard.

### Power Query cleaning checks (about 4 minutes)

This file is already cleaned (it is the clean output of your Python project), so here you are *checking*, not fixing.

**Step 6.** Check the column types. Look at the icon at the top of each column:
- `order_date` should show a **calendar/date** icon.
- `unit_price`, `discount`, and `revenue` should show a **number** icon (123 or a decimal symbol).
- `quantity` should be a whole number.
- Everything else should show **ABC** (text).
- If one is wrong: click the icon, choose the right type (Date, Decimal Number, Whole Number, or Text), and if asked "Replace current step?" click **Replace current**.

**Step 7.** Check for blank (null) values. Click the small drop-down arrow on the `region` column header.
- You should see: the values East, North, South, West, and Unknown — **no (null) entries**. In your Python project you filled the missing regions, and the ones that could not be recovered were labeled "Unknown" ($2,562.25 of revenue — you will see this in the dashboard, and that's fine, it's honest data).

**Step 8.** Check the row count. Look at the bottom-left of the Power Query window — it should say **1,200 rows**. Also right-click the `order_id` column → the values are all unique order IDs (ORD-…); no duplicates to remove in this clean file.

**Step 9.** Click **Close & Apply** (top-left).
- You should see: Power BI loads the data and the **Fields pane** on the right now lists your table with all 11 columns.

**Step 10.** Rename the table to **Orders** (the DAX formulas in `measures.dax` use this name). In the Fields pane, double-click the table name (it is probably called `retail_orders_clean`), type `Orders`, press Enter.

---

## Part 3 — Add the date helper columns (about 3 minutes)

Power BI needs one column that means "the month this order belongs to" so the monthly chart and the month-over-month measure work.

**Step 11.** In the Fields pane, right-click the **Orders** table → **New column**.

**Step 12.** In the formula bar at the top, delete what's there and type exactly:

```
MonthStart = DATE ( YEAR ( Orders[order_date] ), MONTH ( Orders[order_date] ), 1 )
```

Press **Enter**.
- You should see: a new column `MonthStart` in the Fields pane. It turns every order date into the 1st of its month (for example, 2024-11-15 becomes 2024-11-01).
- Set its format: click the `MonthStart` column in the Fields pane, then in the **Column tools** ribbon at the top set **Format** to **Date** (Short Date is fine).

**Step 13.** Right-click **Orders** → **New column** again, and type:

```
MonthName = FORMAT ( Orders[MonthStart], "mmm" )
```

Press **Enter**.
- You should see: a `MonthName` column with values like Jan, Feb, Mar. (This is just the label for the chart. If the months ever appear in the wrong order on a chart, sort `MonthName` by `MonthStart`: select `MonthName`, then Column tools → **Sort by column** → MonthStart.)

---

## Part 4 — Add the DAX measures (about 8 minutes)

Open **`measures.dax`** from this project folder in any text editor and keep it beside Power BI. You will copy 6 measures from it, one at a time. For **each** measure, do this:

1. In the Fields pane, right-click the **Orders** table → **New measure**.
2. In the formula bar, delete the text that's there.
3. Type (or paste) the measure exactly as written in `measures.dax` — name, `=`, and formula.
4. Press **Enter**. The measure appears in the Fields pane with a calculator icon.

**Step 14.** Add these 6 measures, in this order:
1. `Total Revenue`
2. `Total Orders`
3. `Average Order Value`
4. `Category Share %`
5. `Revenue Last Month`, then `MoM Revenue Change %` (MoM uses Revenue Last Month, so order matters)
6. `Best Month Revenue`

**Step 15.** Fix the number formats (this makes the dashboard look right):
- Click `Total Revenue` in the Fields pane → in the **Measure tools** ribbon: Format → **Currency**, and set decimal places to **2**.
- `Total Orders` → Format → **Whole number**, and turn on the **comma style** (thousands separator) if shown.
- `Average Order Value` → **Currency**, 2 decimals.
- `Best Month Revenue` → **Currency**, 2 decimals.
- `Category Share %` → Format → **Percentage**, 1 decimal place.
- `MoM Revenue Change %` → **Percentage**, 1 decimal place.

**Step 16.** Quick check before building charts. Tick the checkbox next to `Total Revenue` in the Fields pane — a bar chart appears with one bar. It should read **$118,568.75**. Tick `Total Orders` too — it should show **1,200**. If both numbers are right, your measures work. Delete this test visual (click it, press Delete) — the real visuals come next.

---

## Part 5 — Build the 5 visuals (about 12 minutes)

First, make the page tidy: click on empty canvas, and in the **Visualizations pane → Format page** (paint roller icon) you can leave everything default for now. You will place visuals on the blank page as follows: title at the top, 3 KPI cards under it, two bar charts in the middle, line chart at the bottom, slicers on the right side.

### Visual 1–3: The KPI cards (Total Revenue, Total Orders, Average Order Value)

**Step 17.** Click on empty canvas so nothing is selected. In the Visualizations pane, click the **Card** icon (looks like a single number card).

**Step 18.** Drag **`Total Revenue`** from the Fields pane into the card's **Fields** well (or just tick its checkbox).
- You should see: a big number — **$118,568.75**. The label underneath says "Total Revenue". 

**Step 19.** With the card selected, use the small white square handles to make it roughly 1/4 of the page wide, and move it to the top-left area under where the title will go. (Exact size doesn't matter.)

**Step 20.** Repeat Steps 17–19 two more times:
- A card with **`Total Orders`** → should show **1,200**.
- A card with **`Average Order Value`** → should show **$98.81**.
- Line the three cards up in a row.

### Visual 4: Bar chart — Revenue by Category

**Step 21.** Click empty canvas. In the Visualizations pane, click the **Bar chart** icon (clustered bar chart — horizontal bars).

**Step 22.** Drag **`category`** into the **Y-axis** field well, and **`Total Revenue`** into the **X-axis** well.
- You should see: 4 horizontal bars — Clothing, Sports, Electronics, Home — with **Clothing longest at $37,046.85**.

**Step 23.** Sort it biggest-first: click the **… (More options)** on the visual's top-right → **Sort axis** → **Total Revenue**, then again → **Sort descending**.

**Step 24 (optional but nice).** Click the visual → Format visual (paint roller) → **Data labels** → turn **On**. Each bar now shows its dollar value. If you want the share as well, drag **`Category Share %`** into the **Tooltip** well of the visual — hovering over Clothing then shows **31.2%**.

### Visual 5: Bar chart — Revenue by Region

**Step 25.** Click empty canvas, add another **Bar chart**.

**Step 26.** Drag **`region`** into the **Y-axis** well and **`Total Revenue`** into the **X-axis** well.
- You should see: West longest at **$30,138.80**, then East, South, North, and a short bar for Unknown. Sort descending the same way as Step 23.

### Visual 6: Line chart — Monthly Revenue Trend

**Step 27.** Click empty canvas. In the Visualizations pane, click the **Line chart** icon.

**Step 28.** Drag **`MonthStart`** into the **X-axis** well and **`Total Revenue`** into the **Y-axis** well.
- **Important:** if Power BI turns MonthStart into a hierarchy (Year/Quarter/Month/Day), click the small arrow next to MonthStart in the X-axis well and choose **MonthStart** (not "Date Hierarchy") so you get exactly 12 points, Jan–Dec 2024.
- You should see: a line that dips in May (**$7,335.45**, the low point) and peaks in November (**$14,948.60**).

**Step 29 (optional).** Drag **`MoM Revenue Change %`** into the line chart's **Tooltip** well — hovering over November then shows **+73.2%** vs October.

### The two slicers — Category and Region

**Step 30.** Click empty canvas. In the Visualizations pane, click the **Slicer** icon.

**Step 31.** Drag **`category`** into the slicer's **Field** well.
- You should see: a list with checkboxes — Clothing, Electronics, Home, Sports.

**Step 32.** Repeat for a second slicer with **`region`**.
- You should see: checkboxes for East, North, South, Unknown, West.

**Step 33.** Test them: click the **Clothing** checkbox. Every number and chart on the page should change to Clothing only (Total Revenue becomes $37,046.85). Click it again to clear. This is the moment the dashboard becomes *interactive* — remember how this felt, you'll describe it in interviews.

**Step 34.** Arrange the page: slicers in a column on the right, the two bar charts side by side in the middle, the line chart wide across the bottom. Drag corners to resize; keep it to one page.

---

## Part 6 — Title, your name, and formatting (about 4 minutes)

**Step 35.** In the top menu, click **Insert → Text box**. Click at the top of the page and type:

**Retail Sales Dashboard**

Select the text, make it **bold**, font size about **24–28**.

**Step 36.** Add a second, smaller text box under it (Insert → Text box) and type:

**Abatar Bhattarai — 2024 retail orders (1,200 orders)**

Font size about 11–12.

**Step 37.** Clean-up pass:
- Click each visual and remove any cluttered default titles you don't need (Format visual → Title → Off) *or* rename them clearly: "Revenue by Category", "Revenue by Region", "Monthly Revenue".
- Make sure the currency numbers show commas and 2 decimals (you set this in Step 15 — if a chart axis shows long decimals, select the visual → Format visual → X/Y axis → **Display units: Thousands** is fine for axes).

**Step 38.** Sanity-check the whole page against these numbers before saving:

| What | Must show |
|---|---|
| Total Revenue card | $118,568.75 |
| Total Orders card | 1,200 |
| Average Order Value card | $98.81 |
| Longest category bar | Clothing — $37,046.85 |
| Longest region bar | West — $30,138.80 |
| Line chart peak | November 2024 — $14,948.60 |
| Line chart low point | May 2024 — $7,335.45 |

If any number is off, the usual cause is a measure typed differently from `measures.dax` — re-check that measure first.

---

## Part 7 — Save the file and export your screenshot (about 3 minutes)

**Step 39.** Click **File → Save As** and save the file as:

**`retail-sales-dashboard.pbix`**

Save it inside this project folder (`power-bi-retail-dashboard`).

**Step 40.** Export the screenshot for GitHub/LinkedIn: click **File → Export → Export to PDF** is *not* what you want. Instead: make the Power BI window full screen on the report page, then press **Windows key + Shift + S**, drag over the full dashboard, and paste/save the snip as a PNG.
- (Alternative: File → Export → Export to PowerPoint → it renders the page as an image you can save.)
- Name the screenshot exactly: **`dashboard.png`** and save it in this same project folder, next to `README.md`. The README already points at that filename, so your GitHub page will show the dashboard at the top.

**Step 41.** Done. Your finished project folder should now contain: `retail_orders_clean.csv`, `measures.dax`, `retail-sales-dashboard.pbix`, `dashboard.png`, and `README.md` — ready to upload to GitHub as a new repo (for example `power-bi-retail-dashboard`), and ready to add to your resume and LinkedIn ("Power BI" under Skills, dashboard as a Featured/project item).

---

## What you just learned (your interview talking points)

- **Loading and checking data in Power Query** — "I load the CSV in Power Query first and check column types and missing values before anything is loaded."
- **Measures vs columns** — "Totals like revenue and AOV are DAX *measures*, so they recalculate when you filter. `MonthStart` is a *column* because I need it on an axis."
- **Why DIVIDE** — "I use DIVIDE instead of / so a filter with no orders shows blank instead of an error."
- **How the share % works** — "Category share uses CALCULATE with ALL to divide each category by the grand total — Clothing is 31.2% of revenue."
- **Month-over-month with DATEADD** — "MoM compares this month to DATEADD minus one month; November jumped 73.2% over October."
- **Slicers make it interactive** — "Category and region slicers filter every visual on the page at once, so a manager can answer their own questions."
- **The story in the data** — "Clothing is the top category, West is the top region, and November is the peak month, so promotions and stock should be planned around those."
- **Honesty point** — "The dataset is practice data from my Retail Sales project; the 'Unknown' region bar is real leftover missing data and I kept it visible instead of hiding it."
