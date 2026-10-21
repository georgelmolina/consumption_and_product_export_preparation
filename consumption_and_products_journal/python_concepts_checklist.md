# Python Concepts Checklist
> Self-study reference — all terms and concepts drawn directly from the scripts in this workspace.
> Goal: write and read these patterns without help, AI, or external tools.

---

## 1. Syntax & Language Basics

- [ ] `#` — inline comment; Python ignores everything after it on that line
- [ ] Indentation — 4 spaces define code blocks (functions, loops, conditionals); never mix tabs and spaces
- [ ] `=` — variable assignment: `x = 5`
- [ ] `:` — ends the header of a function, loop, or conditional block
- [ ] `\` — line continuation in long strings (e.g., Windows paths in YAML)
- [ ] `"""..."""` / `'...'` / `"..."` — string literals (single, double, triple quotes)
- [ ] `f"..."` — f-string (formatted string literal): embeds expressions inside `{}`
- [ ] Format specifier inside f-string: `f"{value:.2f}"` — 2 decimal places
- [ ] `None` — Python's null value (equivalent to NULL in SQL)
- [ ] `True` / `False` — boolean values (capitalized in Python)

---

## 2. Modules & Imports

- [ ] `import module` — loads an entire module: `import yaml`
- [ ] `import module as alias` — loads with a shorthand: `import pandas as pd`
- [ ] Dot notation to access module members: `pd.read_csv()`, `yaml.safe_load()`
- [ ] Standard library modules used: `yaml`
- [ ] Third-party modules used: `pandas`

---

## 3. Data Types & Built-in Structures

### Strings
- [ ] `str` — text data type
- [ ] `.startswith("prefix")` — returns `True` if string begins with the given prefix
- [ ] `.replace(old, new)` — returns a new string with substitution (also works on Series)
- [ ] `.strftime("%Y-%m-%d")` — formats a datetime as a string (ISO 8601 date)
- [ ] f-string expressions: `f"{count / total * 100:.2f}%"`

### Lists
- [ ] `[]` — empty list; `[1, 2, 3]` — list literal
- [ ] `sorted(iterable)` — returns a new sorted list
- [ ] `range(n)` — generates integers from 0 to n-1; used with `range(len(df))`
- [ ] List indexing: `my_list[0]` — first element

### Dictionaries
- [ ] `{}` — empty dict; `{"key": "value"}` — dict literal
- [ ] Key access: `config["consumption_dataset_ready_path"]`
- [ ] Iterating over items: `for prefix, category in my_dict.items():`
- [ ] Dictionary used as a **mapping/lookup table**: `replace_map = {"old": "new"}`

### Sets
- [ ] `set(iterable)` — creates a set of unique values
- [ ] `|` — union: all elements from both sets
- [ ] `-` — difference: elements in left set but not in right
- [ ] `in` — membership test: `if p in my_set:`

---

## 4. List Comprehensions

- [ ] Basic: `[expression for item in iterable]`
- [ ] With condition (ternary): `["✔" if p in my_set else "" for p in all_items]`
- [ ] Used to build DataFrame columns dynamically from a list of values
- [ ] The ternary pattern: `value_if_true if condition else value_if_false`

---

## 5. Control Flow

- [ ] `if condition:` / `elif condition:` / `else:` — branching
- [ ] `for variable in iterable:` — loop over any iterable (list, set, Series, dict)
- [ ] `for key, value in dict.items():` — loop unpacking key-value pairs
- [ ] `return` — exits a function and sends back a value
- [ ] `break` — exits the nearest loop immediately (not used here, but worth knowing)

---

## 6. Functions

- [ ] `def function_name(parameter: type) -> return_type:` — function definition with type hints
- [ ] Type hints: `: str` on parameter, `-> str` on return — documentation only, not enforced
- [ ] `"""Docstring."""` — single-line docstring immediately after `def`; describes what the function does
- [ ] `pd.isna(value)` — checks if a value is NaN/None; used inside a function as a guard clause
- [ ] `.apply(function)` — applies a function row-by-row to a Series: `df["col"].apply(assign_category)`
- [ ] Functions as first-class objects: you can pass a function name (without `()`) as an argument

---

## 7. File Handling

- [ ] `open(path, mode, encoding)` — opens a file; returns a file object
- [ ] `"r"` mode — read-only
- [ ] `encoding="utf-8"` — specify character encoding to avoid special character errors
- [ ] `with open(...) as f:` — context manager; automatically closes the file after the block
- [ ] `yaml.safe_load(f)` — parses a YAML file into a Python dictionary

---

## 8. YAML Basics (`config.yaml`)

- [ ] Key-value syntax: `key: "value"`
- [ ] String values in quotes: `"C:\\path\\to\\file.xlsx"`
- [ ] `\\` in YAML strings — escaped backslash for Windows paths
- [ ] `yaml.safe_load()` — reads a `.yaml` file into a Python `dict`
- [ ] Dictionary key access after loading: `config["key_name"]`

---

## 9. Pandas — Reading & Writing

- [ ] `pd.read_excel(path, engine="openpyxl")` — reads `.xlsx` files
- [ ] `pd.read_csv(path, encoding="utf-8")` — reads `.csv` files
- [ ] `df.to_excel(path, index=False)` — exports to `.xlsx`; `index=False` omits the row numbers
- [ ] `df.to_csv(path, index=False, encoding="utf-8")` — exports to `.csv`
- [ ] `df.copy()` — creates an independent copy to avoid mutating the original

---

## 10. Pandas — Inspection

- [ ] `df.info()` — prints column names, dtypes, and non-null counts
- [ ] `df.shape` — tuple `(rows, columns)`
- [ ] `df.head()` — first 5 rows
- [ ] `df.dtypes` — dtype of each column
- [ ] `df.columns.to_list()` — list of column names (note: `.to_list` without `()` is a bug — use `.to_list()`)
- [ ] `df["col"].unique()` — array of distinct values in a column
- [ ] `df["col"].isna().sum()` — count of NaN values in a column
- [ ] `display(obj)` — Jupyter-specific; renders rich output (tables, lists)

---

## 11. Pandas — Selection & Filtering

- [ ] `df["col"]` — select a single column (returns a Series)
- [ ] `df[["col1", "col2"]]` — select multiple columns (returns a DataFrame)
- [ ] Boolean mask: `df[df["col"] == value]`
- [ ] Compound condition with `&`: `df[(condition1) & (condition2)]` — each condition in `()`
- [ ] `!=` — not equal operator: `df["plant"] != "P_B1"`
- [ ] `.isin(collection)` — filters rows where column value is in a set or list
- [ ] `.dropna()` — removes NaN values from a Series before further processing
- [ ] `df.loc[mask, "col"]` — label-based selection with a boolean mask; used to update specific rows
- [ ] `.str.startswith("prefix")` — string accessor method on a Series; returns boolean mask

---

## 12. Pandas — Transformation

- [ ] `.replace(dict)` — replaces values in a Series using a mapping dictionary
- [ ] `.replace(old, new)` — single-value replacement
- [ ] `df["col"] = ...` — assign/overwrite a column
- [ ] `df.insert(position, "col_name", values)` — insert a column at a specific position
- [ ] `df.assign(new_col=expression)` — adds new columns inside a method chain; returns a new DataFrame
- [ ] `df.sort_values("col")` — sorts by a column ascending by default
- [ ] `df.sort_values(["col1", "col2"])` — sorts by multiple columns
- [ ] `df.reset_index(drop=True)` — resets integer index after filtering or sorting; `drop=True` discards old index
- [ ] `df.drop_duplicates()` — removes duplicate rows
- [ ] `df[column_list]` — reorder (and subset) columns using a list

---

## 13. Pandas — Aggregation & Grouping

- [ ] `.groupby("col")` — groups rows by unique values in a column
- [ ] `.groupby(["col1", "col2"])` — multi-column groupby
- [ ] `.size()` — counts rows in each group (returns a Series)
- [ ] `.sum()` — sums values within each group
- [ ] `.reset_index(name="col_name")` — converts groupby result back to a DataFrame, renaming the aggregated column
- [ ] `.merge(other_df, on="col")` — joins two DataFrames on a shared column (default: inner join)

---

## 14. Pandas — Datetime

- [ ] `pd.to_datetime(series_or_value)` — converts strings or objects to datetime64
- [ ] `.dt` accessor — access datetime properties on a Series: `.dt.year`, `.dt.month`
- [ ] `.dt.normalize()` — strips the time component (sets time to 00:00:00)
- [ ] `.dt.date` — converts datetime64 to Python `date` objects (no time, no timezone)
- [ ] `.dt.strftime("%Y-%m-%d")` — formats datetime as `"YYYY-MM-DD"` string
- [ ] `pd.to_datetime("2024-06-28")` — converts a literal string to a single Timestamp for comparison

---

## 15. Pandas — DataFrame Constructor

- [ ] `pd.DataFrame({"col1": list1, "col2": list2})` — creates a DataFrame from a dictionary of equal-length lists
- [ ] Column order follows dictionary insertion order (Python 3.7+)

---

## 16. Operators & Expressions

| Operator | Meaning | Example |
|----------|---------|---------|
| `==` | Equal | `df["col"] == "value"` |
| `!=` | Not equal | `df["plant"] != "P_B1"` |
| `&` | Bitwise AND (pandas boolean) | `(cond1) & (cond2)` |
| `\|` | Set union | `prod_set \| cons_set` |
| `-` | Set difference | `prod_set - cons_set` |
| `in` | Membership test | `if p in my_set:` |
| `/` | Division | `count / total * 100` |
| `*` | Multiplication | `count * 100` |
| `:.2f` | Float format spec | `f"{value:.2f}"` |

---

## 17. Built-in Functions Used

- [ ] `set()` — converts iterable to a set of unique values
- [ ] `sorted()` — returns a sorted list from any iterable
- [ ] `range(n)` — integer sequence from 0 to n-1
- [ ] `len(df)` — number of rows in a DataFrame (or length of any sequence)
- [ ] `print(value)` — prints to console; used with f-strings for verification steps
- [ ] `open(path, mode, encoding)` — opens a file
- [ ] `display()` — Jupyter built-in for rich rendering

---

## Quick Reference: Patterns Used in This Notebook

```python
# Load config
with open("../config/config.yaml", "r", encoding="utf-8") as f:
    config = yaml.safe_load(f)

# Read file using config path
df = pd.read_excel(config["key"], engine="openpyxl")

# Set operations for product comparison
prod_set = set(df["col"].dropna().unique())
cons_set = set(other_df["col"].dropna().unique())
all_items = sorted(prod_set | cons_set)

# List comprehension for DataFrame column
"in_prod": ["✔" if p in prod_set else "" for p in all_items]

# Replace using a dict map
df["col"] = df["col"].replace({"old_value": "new_value"})

# Boolean mask with multiple conditions
result = df[(df["vol"] == 7.0) & (df["date"] == pd.to_datetime("2024-06-28"))]

# Custom function + apply
def assign_category(product: str) -> str:
    if pd.isna(product):
        return "uncategorized"
    for prefix, cat in category_map.items():
        if product.startswith(prefix):
            return cat
    return "uncategorized"

df["product_category"] = df["product"].apply(assign_category)

# Groupby + aggregate + reset
result = (
    df.groupby(["col1", "col2"])["value"]
    .sum()
    .reset_index(name="total")
)

# Export with date formatting
export = df.copy()
export["date"] = export["date"].dt.strftime("%Y-%m-%d")
export.to_csv(path, index=False, encoding="utf-8")
```
