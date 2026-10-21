# Project: consumption_and_products_journal

**"Compact Folders"** feature when a folder (like `notebooks`) contains *only one* subfolder (like `__pycache__`) and no loose files, it collapses them into a single line (`notebooks\__pycache__`) to save vertical space.

As soon as you added the folder `Cleaning_24` in your second image, VS Code broke them apart automatically because `notebooks` now had more than one item inside.

If you hate this collapsing behavior and want it to always look like the **"corregido"** image (with a clean, traditional nested tree), you can disable it in your workspace settings without writing any code.

---

### How to fix it visually using the UI

1. Open your settings using the keyboard shortcut: **`Ctrl + ,`** (comma).
2. In the search bar at the top, type: **`compact folders`**.
3. Look for the setting called **Explorer: Compact Folders**.
4. **Uncheck** the box.

---

## Issue: the date column order random. Why do hours:minutes:seconds appear in some DataFrames but not others?

**Context:** Two DataFrames loaded in the same notebook show different behaviors on the `date` column — one is ordered oldest→newest with no time component, the other is ordered newest→oldest (within each year) and includes hours:minutes:seconds.

---

### Why is the row order "random" by default?

A pandas DataFrame preserves the **physical row order of the source file**. It does not sort by any column automatically. So whatever order the rows were written in the source file (Excel or CSV) is the order you get.

- If the source file was exported from a system that writes records as they are created (e.g., a production ERP), the order depends entirely on how that system outputs data — often by entry date descending, or grouped by year then descending within each year.
- If a different pipeline sorted or aggregated the data before saving it, you get a different order.

**The rule:** pandas never reorders rows on read unless you explicitly call `.sort_values()`.

This comes from **how the date was stored in the source file**, not from pandas itself.

| Source scenario                                                     | What pandas reads                                        |
| ------------------------------------------------------------------- | -------------------------------------------------------- |
| Excel cell formatted as `Date` (e.g., `05/01/2024`)                 | Reads as `datetime64` with time `00:00:00` — but display |
| Excel cell formatted as `Date + Time` or stored as a full timestamp | Reads as `datetime64` with the actual time component     |
| CSV column stored as `"2024-01-05 08:32:00"`                        | Reads as `datetime64` with time                          |
| CSV column stored as `"2024-01-05"`                                 | Reads as `datetime64` with time `00:00:00`               |

Even when the time is `00:00:00`, pandas internally stores it as a full `datetime64`. Whether you *see* the time part depends on how you display the column (`.head()`, Excel export, etc.).

**The key insight:** the difference between your two DataFrames is not pandas — it is that one source file stored pure dates and the other stored full timestamps.

---

### How to normalize and sort (the fix applied in this notebook)

```python
# Strip time and sort chronologically.
df["date"] = pd.to_datetime(df["date"]).dt.normalize()   # sets time to 00:00:00
df = df.sort_values("date").reset_index(drop=True)
```

- `.dt.normalize()` — truncates the time to midnight, making all values pure dates.
- `.sort_values("date")` — enforces chronological order regardless of source order.
- `.reset_index(drop=True)` — resets the row index to match the new order (and re-assign ID columns if needed).

---

### Why does hh:mm:ss still appear in Excel, SQL Server and Power BI even after normalize()?

Even after `.dt.normalize()`, the column type inside pandas is still `datetime64[ns]`. That means the value is stored as `2024-01-05 00:00:00`. The zeros are hidden in the notebook display, but when you export to Excel, the cell is written as a **DATETIME** value — and Excel, SQL Server and Power BI all show the time component because it is technically there.

**The format `YYYY-MM-DD` (date only, no time) in every downstream tool requires the column to be a Python `date` object, not a `datetime64`.**

---

### The correct export pattern

Never modify the working DataFrame before exporting. Use `.copy()` so the `datetime64[ns]` column is preserved in memory for any further analysis in the notebook.

```python
export_df = df.copy()
export_df["date"] = export_df["date"].dt.date   # converts to Python date object
export_df.to_excel("output.xlsx", index=False)
```

- `.copy()` — creates an independent copy. Changes to `export_df` do not affect the original `df`.
- `.dt.date` — converts each `datetime64` value to a Python `date` object (e.g. `datetime.date(2024, 1, 5)`). openpyxl writes this as a pure **DATE** cell in Excel — no time component.
- Excel, SQL Server and Power BI all read a pure DATE cell as `YYYY-MM-DD` with no hours, minutes or seconds.

---

### Summary: which type to use and when

| Situation                                            | Type to use             | Why                                             |
| ---------------------------------------------------- | ----------------------- | ----------------------------------------------- |
| Working in pandas (filtering, groupby, calculations) | `datetime64[ns]`        | Full pandas datetime functionality              |
| Exporting to Excel / loading into SQL / Power BI     | `date` (via `.dt.date`) | Written as pure DATE cell, no time component    |
| Normalizing (removing time from datetime64)          | `.dt.normalize()`       | Sets time to 00:00:00 but keeps datetime64 type |

---


Why in export_consumption_csv and export products, date is still a string or object?

The date column is **supposed to be** a string/object type at that point. Here's why:

**`strftime()` converts datetime → string**

When you call `.dt.strftime("%Y-%m-%d")`, you're explicitly converting the datetime object to a **string representation**. That's what strftime does—it returns a formatted string.

```python
export_consumption_csv["date"] = export_consumption_csv["date"].dt.strftime("%Y-%m-%d")
# strftime returns: "2024-01-05" (string)
```

In pandas, strings are stored as `object` dtype, so you'll see:
- **Before strftime:** `datetime64[ns]`
- **After strftime:** `object` (because strings are objects in pandas)

**Why this is correct:**

1. **CSV export requires strings** — CSV files are plain text, so dates must be strings
2. **MySQL accepts this format** — MySQL's `DATE` columns accept `YYYY-MM-DD` strings directly without casting
3. **This is the intended behavior** — you want the CSV to contain readable date strings like `"2024-01-05"`, not serialized datetime objects

If you wanted to keep the datetime type in memory (for continued analysis), you wouldn't use strftime. But since you're exporting to CSV, converting to string is the right approach.

Also note: In the last cell, you're checking `export_products.dtypes`, but you created `export_products_csv`. That's why it might look like nothing changed—you're inspecting the original dataframe instead of the exported one.