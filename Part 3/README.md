# Maji Ndogo Water Services: Part 3, The Auditor's Report

Part 3 of the Maji Ndogo SQL project. An independent auditor re-visited a sample of water sources and re-scored their quality. This part loads the auditor's data into MySQL, compares it with the surveyors' original scores, and uses the results to find employees whose records may be unreliable.

## Contents

- [Background](#background)
- [What this notebook does](#what-this-notebook-does)
- [Data](#data)
- [Setup](#setup)
- [Running the notebook](#running-the-notebook)
- [Key results](#key-results)
- [Files](#files)

## Background

President Naledi appointed an auditor to investigate anomalous records in the `water_quality` table. The auditor re-visited a random subset of **1,620** records and re-recorded each source's quality score. The auditor also collected statements from citizens near the sources to understand why scores differed.

The question for this part: **do the surveyors' scores match the auditor's, and where they don't, who recorded them?**

## What this notebook does

| Step | Description |
|------|-------------|
| 1 | Connect to MySQL using credentials from a `.env` file (SQLAlchemy and PyMySQL) |
| 2 | Read `Auditor_report.csv` with pandas |
| 3 | Create the `auditor_report` table and load the 1,620 rows |
| 4 | Join `auditor_report` to `visits` and `water_quality` through `location_id` and `record_id` |
| 5 | Keep only first visits (`visit_count = 1`) so each audited location appears once |
| 6 | Split the records into matching and mismatching scores |
| 7 | Add the source type from `water_source` and the employee from `employee` |
| 8 | Count each employee's mistakes and compare them with the average |
| 9 | Review the citizens' statements for the flagged employees, including searching for mentions of cash |

## Data

### `auditor_report` (loaded from the CSV)

| Column | Type | Description |
|--------|------|-------------|
| `location_id` | VARCHAR(32) | Unique ID of the source location (joins to `visits.location_id`) |
| `type_of_water_source` | VARCHAR(64) | Type of source the auditor re-examined |
| `true_water_source_score` | INT | The auditor's quality score |
| `statements` | VARCHAR(255) | Statements from citizens near the source |

The source types in the file are `well` (881), `shared_tap` (295), `tap_in_home_broken` (272) and `river` (172).

### Tables from earlier parts

| Table | Used for |
|-------|----------|
| `visits` | Links a location to a record, an employee and a visit number |
| `water_quality` | The surveyors' `subjective_quality_score` |
| `water_source` | The surveyed source type, used to cross-check the auditor's |
| `employee` | Employee names, joined on `assigned_employee_id` |

The full data dictionary is in `Data_dictionary__auditor_report.pdf`. The schema is in the MySQL Workbench model `maji_ndogo_model_AND_auditor_report.mwb`.

> **Note:** the CSV is **semicolon-delimited** (`;`), not comma-delimited, and its header has a space after each separator. Read it with `sep=";"` and `skipinitialspace=True`, otherwise pandas raises a `ParserError`, because the statements contain commas.

## Setup

**Requirements**

- Python 3.10 or later and Jupyter
- MySQL 8.0 with the `md_water_services` database from Parts 1 and 2
- Python packages:

```bash
pip install pandas sqlalchemy pymysql python-dotenv jupysql
```

**Credentials**

Create a `.env` file in the same folder as the notebook:

```
DB_HOST=localhost
DB_USER=your_username
DB_PASSWORD=your_password
DB_NAME=md_water_services
```

`.env` is listed in `.gitignore` so your password is never committed. Share a `.env.example` with placeholder values instead.

## Running the notebook

1. Open `part3.ipynb`.
2. Edit the `path` variable in the loading cell so it points to `Auditor_report.csv` on your machine.
3. Choose **Run All**. The cells depend on each other in order: the connection first, then the CSV load, then the queries.

The table-creation cell begins with `DROP TABLE IF EXISTS auditor_report`, so re-running it resets the table instead of duplicating rows. After loading, the check cell should print `rows in table: 1620 | rows in df: 1620`.

## Key results

- **Joining on `location_id` alone returns 2,698 rows** for the 1,620 audited locations, because some locations were visited more than once. Filtering on `visit_count = 1` gives exactly one row per location.
- Of the 1,620 first-visit records, **1,518 scores match** the auditor's and **102 do not**.
- The average number of mistakes per employee among the mismatches is **6.0**. Employees above that average were investigated further.
- For the four employees investigated, the citizens' statements were reviewed, including those that mention cash payments.

The data is from a training dataset (Explore AI Academy), and the people and places are fictional.

## Files

```
.
├── part3.ipynb                                   # analysis notebook
├── Auditor_report/
│   └── Auditor_report.csv                        # auditor's re-scored records (1,620 rows, ";" delimited)
├── .env.example                                  # credential template (copy to .env)
├── .gitignore
└── README.md
```
