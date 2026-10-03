# Maji Ndogo Water Services: Part 1, Beginning a Data-Driven Journey

## Overview
Maji Ndogo is a fictional country facing a water access crisis. A large survey
collected data on water sources, visits, queue times, water quality and
pollution across the country. In Part 1 I get to know that data: how the
database is structured, what each table holds, and whether the data is clean
enough to trust before any deeper analysis.

## Objectives
- Understand the structure of the `md_water_services` database
- Explore the key tables and the types of water sources
- Identify long queue times in the survey data
- Check data integrity in the water quality and pollution tables
- Correct inconsistent records safely

## Tech Stack
- MySQL 8.0
- Python 3 with `mysql.connector`
- Jupyter Notebook

## Database Structure
| Table | Description |
| `data_dictionary` | Describes each column in every table |
| `employee` | Surveyors and staff who collected the data |
| `global_water_access` | Country-level water access statistics |
| `location` | Where each water source is located |
| `visits` | Each visit made to a water source |
| `water_quality` | Quality scores recorded per visit |
| `water_source` | Water sources and their types |
| `well_pollution` | Contamination test results for wells |

## Methodology

### 1. Exploring the database
- Listed the tables and previewed each with `SHOW TABLES` and `SELECT * ... LIMIT`
- Used `data_dictionary` to understand column meanings

### 2. Water sources
- Found the distinct source types using `SELECT DISTINCT type_of_water_source`
- Types found: [list them]

### 3. Queue times
- Filtered `visits` for `time_in_queue > 500` minutes
- Cross-referenced the source IDs with `water_source` to see which types have the longest queues
- Finding: [which source types dominate]

### 4. Data integrity checks
- **Quality scores:** Checked for records with a score of 10 on a repeat visit
  (`visit_count > 1`), which should not happen for a second visit. Found: [n] records
- **Pollution results:** Checked for rows marked `Clean` where the biological
  contamination was above the safe threshold (0.01)

### 5. Correcting the data
- Created `well_pollution_copy` and tested the fixes there first
- Fixed mislabeled descriptions (for example, `Clean Bacteria: E. coli` became `Bacteria: E. coli`)
- Updated wrongly labeled results to `Contaminated: Biological`
- Applied the verified updates to `well_pollution`, then dropped the copy table

## Key Findings
1. [Finding 1]
2. [Finding 2]
3. [Finding 3]

## How to Run
1. Install MySQL 8.0 and load the `md_water_services` database
2. Install dependencies: `pip install python-dotenv mysql-connector-python jupyter`
3. Copy `.env.example` to `.env` and fill in your MySQL credentials
4. Run the notebook cells in order

## Next Steps
Part 2 moves on to deeper analysis: [e.g. joining tables, integrating the report]

## Author
Joshua Nduva