# Maji Ndogo Water Services — Part 2: Cleaning & Clustering the Data

SQL analysis of the `md_water_services` database, run from a Jupyter notebook using MySQL and Python. Part 2 cleans the employee data and groups (clusters) the survey records to understand how people in Maji Ndogo access water and how long they wait for it.

## Contents

- [What this part covers](#what-this-part-covers)
- [Tech stack](#tech-stack)
- [Setup](#setup)
- [Running the notebook](#running-the-notebook)
- [Key findings](#key-findings)
- [Notes](#notes)
- [Project structure](#project-structure)

## What this part covers

| Section | What happens |
|---|---|
| **Employee data** | Generates `first.last@majindogogov.com` emails, trims stray spaces from phone numbers (13 → 12 characters), counts employees per town, and finds the 5 employees with the most visits |
| **Location data** | Records per town and province, and the rural vs urban split |
| **Water sources** | Sources by type, average and total people served, share of the population per type, and a ranking of which source types to fix first (`RANK`, `DENSE_RANK`, `ROW_NUMBER`) |
| **Queue times** | Average queue time by day and by hour, and a day × hour pivot table built with `CASE WHEN` |
| **Project 7** | Questions and answers on source types, shared taps, rural/urban share, population served and queue times |

## Tech stack

- MySQL 8.0
- Python 3 with Jupyter Notebook
- SQLAlchemy + PyMySQL (database connection)
- `jupysql` (`%sql` and `%%sql` magics)
- `python-dotenv` (credentials from a `.env` file)

## Setup

**1. Install the Python packages**

```bash
pip install jupyter jupysql sqlalchemy pymysql python-dotenv
```

**2. Make sure the database exists**

The notebook expects a MySQL database called `md_water_services` running on `localhost:3306`. Load it from the `md_water_services.sql` file used in Part 1.

**3. Create a `.env` file** in the same folder as the notebook (and keep it out of Git):

```env
DB_USER=your_mysql_user
DB_PASSWORD=your_mysql_password
DB_HOST=localhost
DB_NAME=md_water_services
```

## Running the notebook

```bash
jupyter notebook part2.ipynb
```

Run the cells from top to bottom. The first code cell loads the `.env` file, creates the connection and enables the `%sql` magics.

## Key findings

| Finding | Result |
|---|---|
| People served in total | ~27.6 million |
| Most common source type | Wells (17,383 sources) |
| Source type serving the most people | Shared taps: ~11.9 million people (43% of the population) |
| Average people per shared tap | ~2,071 |
| Rural locations | ~60% of all locations |
| Average queue time (visits with a queue) | ~123 minutes |
| Longest queues | Saturdays (~246 min on average) |
| Shortest queues | Sundays (~82 min on average) |
| Busiest period on weekdays | Early morning, 06:00–08:00 |

## Notes

- **The notebook changes data.** Two `UPDATE` statements write to the `employee` table (emails and trimmed phone numbers). Running them again is safe because they give the same result.
- **Queue times exclude zero waits.** Queue-time queries filter on `time_in_queue > 0`.
- **Priority ranking skips `tap_in_home`.** It is the best-case source, so it is left out of the "what to fix first" ranking.
- **Pivot queries use `ELSE NULL`, not `ELSE 0`.** Inside `AVG(CASE WHEN ...)`, `ELSE 0` would count other days as zero-minute waits and give averages that are far too low. With `NULL`, each column averages only its own day.
- **Never commit credentials.** Keep `.env` in `.gitignore`.


```
