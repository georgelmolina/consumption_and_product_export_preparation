# Consumption & Products — Data Preparation Project

## Overview

This project implements a data preparation pipeline for two independent datasets: **consumption_dataset_ready** and **products_years.csv**. The pipeline covers data ingestion, validation, cleaning, and export to both XLSX and CSV. 

---

## Objectives

- Validate and reconcile product names across both datasets to ensure referential consistency.
- Apply ETL corrections for known data entry anomalies (e.g., product code misspellings, missing decimal points in product resistance values).
- Export each cleaned dataset independently to CSV and load into a MySQL database.

---

## Project Structure

```
consumption_and_products_project/
├── config/
│   └── config.yaml              # Centralized file paths configuration
├── data/
│   ├── import/                  # Raw source files (input)
│   ├── processed/               # Intermediate outputs
│   └── export/                  # Final cleaned datasets ready for export
├── docs/
│   ├── Considerations.md        # Domain knowledge and business context
│   └── Discoveries.md           # Findings and data anomalies discovered
├── notebooks/                   # Jupyter notebooks for exploratory and pipeline work
├── outputs/
│   ├── figures/                 # Charts and visual outputs
│   └── report/                  # Summary reports
├── scripts/                     # Standalone Python scripts
├── src/
│   ├── connection.py            # MySQL database connection handler
│   └── utils.py                 # Shared utility functions
├── requirements.txt
└── README.md
```

---

## Datasets

| Dataset                     | Source Format | Description                                     |
| --------------------------- | ------------- | ----------------------------------------------- |
| `consumption_dataset_ready` | `.xlsx`       | Consumption records extracted from Sage 200 ERP |
| `products_years`            | `.csv`        | Product catalogue with yearly attributes        |

Imported from consumption_workspace & products_workspace

## Pipeline Steps

1. **Ingestion** — Load both datasets using paths defined in `config/config.yaml`.
2. **Sanity Check** — Inspect shape, dtypes, and null distribution.
3. **Product Name Validation** — Cross-reference product names between datasets and flag mismatches.
4. **Data Cleaning** — Apply standardized name corrections and fix known data entry anomalies.
5. **Export** — Write each cleaned dataset to CSV and load into MySQL independently.

---

## Tech Stack

| Tool             | Purpose                              |
| ---------------- | ------------------------------------ |
| Python 3.11      | Core language                        |
| pandas           | Data manipulation                    |
| openpyxl         | Excel file I/O                       |
| PyYAML           | Configuration management             |
| Jupyter Notebook | Exploratory and pipeline notebooks   |
| MySQL            | Target database for cleaned datasets |

---

## Setup

1. Clone the repository.
2. Create and activate a virtual environment (Miniconda recommended). 
   - conda create -y -n consumption_and_products__env python=3.11
   - conda activate consumption_and_products__env
3. Install dependencies:
   
   ```bash
   pip install -r requirements.txt
   ```
4. Update file paths in `config/config.yaml` to match your local environment.

---

## Configuration

All input file paths are managed centrally in `config/config.yaml`. Update this file before running any notebook or script — no hardcoded paths exist outside of it.
