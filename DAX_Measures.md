# DAX Measures & Data Model

This document explains the calculations and data preparation behind the Blinkit Sales Dashboard.

## Data Model

| Table | Type | Purpose |
|-------|------|---------|
| `BlinkIT Grocery Data` | Imported from Excel | Main fact table: 8,523 rows, 12 columns (item, outlet, sales, rating) |
| `metrics` | Field Parameter | Lets the user switch the chart metric between Total Sales, Average Sales, Total Items, and Average Rating |

## DAX Measures

### 1. Total Sales
```dax
Total sales = SUM('BlinkIT Grocery Data'[Sales])
```
Adds up the sales value of all items in the current filter context. Format: `$#,##0`.

### 2. Average Sales
```dax
Average sales = AVERAGE('BlinkIT Grocery Data'[Sales])
```
Average sales value per item record. Because it is a measure, it recalculates for whatever slicer or visual it sits in (item type, outlet type, location tier, and so on). Format: `$#,##0`.

### 3. Total Items
```dax
Total items = COUNT('BlinkIT Grocery Data'[Sales])
```
Counts the number of item sales records in the current filter context.

### 4. Average Rating
```dax
Average rating = AVERAGE('BlinkIT Grocery Data'[Rating])
```
Average customer rating on a 1–5 scale.

## Field Parameter: Dynamic Metric Switch

The `metrics` table is a Power BI field parameter. It lets one visual show any of the four measures, chosen by the user from a slicer.

```dax
metrics = {
    ("Total sales",    NAMEOF('BlinkIT Grocery Data'[Total sales]),    0),
    ("Average sales",  NAMEOF('BlinkIT Grocery Data'[Average sales]),  1),
    ("Total items",    NAMEOF('BlinkIT Grocery Data'[Total items]),    2),
    ("Average rating", NAMEOF('BlinkIT Grocery Data'[Average rating]), 3)
}
```
This avoids building four separate charts and keeps the dashboard clean and interactive.

## Data Cleaning (Power Query)

| Step | What it does |
|------|--------------|
| Promote Headers | Uses the first row of the sheet as column names |
| Change Type | Sets correct data types (text, whole number, decimal) for all 12 columns |
| Replace Value: `LF` → `Low Fat` | Standardizes an inconsistent label in `Item Fat Content` |
| Replace Value: `reg` → `Regular` | Standardizes an inconsistent label in `Item Fat Content` |

The raw data has five spellings of two categories (`Regular`, `Low Fat`, `low fat`, `LF`, `reg`). After cleaning, it is reduced to two (Low Fat and Regular).

## Setup Note

The Power Query source is set to a local file path. When you open the `.pbit` file, Power BI will ask for the location of `BlinkIT_Grocery_Data.xlsx`. Point it to the copy in this repo.
