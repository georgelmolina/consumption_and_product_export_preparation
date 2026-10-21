## Project: consumption_and_products_journal

## `.replace()` vs `.map()` on a pandas Series.

**Context:** Correcting wrong product names in `products_years` during the ETL/cleaning phase of the merge notebook.

### The rule

| Method           | Behavior on values NOT in the dict |
| ---------------- | ---------------------------------- |
| `.replace(dict)` | Leaves them **unchanged**          |
| `.map(dict)`     | Converts them to **`NaN`**         |

### When to use each

- Use `.replace()` when you want a **partial substitution** — only fix specific values, leave everything else as-is. This is the right tool for name correction maps.
- Use `.map()` when you want a **full transformation** — every value in the column must be covered by the dict (e.g., encoding categories to numbers).

### Example

```python
product_name_map = {
    "H-100/B/20":     "H-100-B-20",
    "HA-25-F-15-IIA": "HA-25-F-15",
    "HF-35-B-20":     "HF-3.5-B-20",
    "HF-35-B-20-XC2": "HF-3.5-B-20-XC2",
}

# Safe: unlisted products are untouched.
products_years["product"] = products_years["product"].replace(product_name_map)
```

### Key takeaway

`.replace()` is surgical — it only changes what you tell it to. `.map()` is total — silence means `NaN`.

---

## Collapsible / Scrollable output in VS Code notebooks — 2026-06-08

**Context:** When does VS Code collapse a cell output and show the scrollable toggle?

### The two conditions

1. The output must be a **DataFrame** (not `print()`, not a numpy array).
2. The DataFrame must have **enough rows** to exceed VS Code's height threshold (~15–20 rows depending on screen). Below that, it collapses but without the scrollable toggle.

| Output type                    | Collapses? | Scrollable toggle? |
| ------------------------------ | ---------- | ------------------ |
| `DataFrame` with many rows     | ✔ Yes      | ✔ Yes              |
| `DataFrame` with few rows      | ✔ Yes      | ✘ No — too short   |
| `print()`                      | ✘ No       | ✘ No               |
| numpy array (e.g. `.unique()`) | ✘ No       | ✘ No               |

### How to trigger the scrollable toggle manually

If the DataFrame is short and the toggle doesn't appear automatically: **right-click on the output → "Toggle Scrollable"**.

### How to trigger it with code

Make the **last line of the cell return a DataFrame** — no `print()`, no assignment.

```python
# Already a DataFrame — just return it.
product_comparison
volume_comparison

# Wrap a list/array in a DataFrame first.
pd.DataFrame(sorted(consumption_merged_ready["product"].unique()), columns=["product"])
```

This is the way to display the entire output with option to scroll a collapse output or open in a text editor. 

for p in sorted(products_years["product"].unique()):
    print(p)

Counting a value in a column and get a complete output.
    count = (products_years["product"] == "no_registered").sum()
total = len(products_years)
print(f"no_registered: {count} rows out of {total} ({count / total * 100:.2f}%)")

Output.
no_registered: 1 rows out of 9012 (0.01%)

### Key takeaway

Two requirements for the scrollable toggle: **DataFrame output** + **enough rows**. If rows are few, use right-click → Toggle Scrollable. Never use `print()` for tabular or list data in notebooks.

---

## Hardcoded paths in notebooks — why they break reproducibility — 2026-06-12

**Context:** Portfolio polish audit on `data_cleaning_and_export_preparation.ipynb`. Two intermediate `.to_excel()` calls were using absolute machine-specific paths instead of `config.yaml`.

### The problem

A hardcoded absolute path like `r"C:\Users\User\Desktop\..."` only works on the machine where it was written. Anyone else who clones the project — or even yourself on a different machine — will get a `FileNotFoundError` immediately. It also contradicts any README claim of centralized configuration.

### The rule

> **All file paths — import, processed, and export — must live in `config.yaml`.** No exceptions in notebooks or scripts.

### Pattern to follow

**config.yaml:**

```yaml
# Processed intermediate outputs.
all_unique_products_comparison_path: "...data/processed/all_unique_products_comparison.xlsx"
products_only_in_prod_path: "...data/processed/products_only_in_prod.xlsx"
```

**Notebook cell:**

```python
# Load config once at the top of the notebook.
with open("../../config/config.yaml", "r", encoding="utf-8") as f:
    cfg = yaml.safe_load(f)

# Use the key — never the raw path.
df.to_excel(config["all_unique_products_comparison_path"], index=False)
```

### Key takeaway

Three categories of paths must all go through `config.yaml`: **import** (raw sources), **processed** (intermediate outputs), and **export** (final deliverables). If a path appears as a string literal inside a notebook cell, it is a bug. **¡¡the same rule applies. The final export cells also have hardcoded absolute paths. Let me check their current state.!!**

---

## "Open Editors" in VS Code Explorer — 2026-06-13

**Context:** An "Open Editors" section appeared at the top of the Explorer sidebar, looking like it might be part of the project structure.

### What it is

"Open Editors" is a **VS Code UI panel**, not a folder or file. It lists all files currently open in editor tabs. It has nothing to do with the project on disk.

### How to hide it

Right-click anywhere in the Explorer sidebar → uncheck **Open Editors**.

### Key takeaway

If something appears in the Explorer sidebar but has no corresponding folder or file on disk, it is a VS Code panel feature — not part of the project.

---

## `.gitignore` design decisions — 2026-06-13

**Context:** Portfolio polish audit. The `.gitignore` was empty; this note explains why the final version is correct for this project.

### Why each section is included

| Section                                 | Reason                                                                                                                                                                                          |
| --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `__pycache__/`, `*.py[cod]`, `*.pyo`    | Python auto-generates these bytecode files when importing modules. They are machine-specific and rebuilt on every run — committing them adds noise and can cause conflicts across environments. |
| `.venv/`, `env/`, `venv/`, `ENV/`       | Virtual environment folders are large, machine-specific, and fully reproducible from `requirements.txt`. They must never be committed.                                                          |
| `.ipynb_checkpoints/`                   | Jupyter auto-saves checkpoint files next to every notebook. They are internal recovery files, not part of the project logic.                                                                    |
| `dist/`, `build/`, `*.egg-info/`        | Packaging artifacts generated if the project is ever distributed. Not relevant now, but standard to exclude.                                                                                    |
| `.vscode/`                              | VS Code workspace settings are personal/machine-specific. Other contributors should configure their own environment.                                                                            |
| `.DS_Store`, `Thumbs.db`, `desktop.ini` | OS-generated metadata files (macOS and Windows). Irrelevant to the project and pollute the repository.                                                                                          |

### Why data files are NOT excluded

All data in this project (`data/import/`, `data/export/`, `data/processed/`) is already anonymized at the source. No personal, confidential, or sensitive information exists in the files. Therefore, committing the data is appropriate — it makes the project fully self-contained and reproducible for any reviewer who clones it.

### Key takeaway

A `.gitignore` should exclude **tooling artifacts** (bytecode, checkpoints, OS files, environments) and **sensitive data**. When data is already anonymized, excluding it would break reproducibility for no reason.