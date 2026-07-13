# Tech Layoffs (2020–2023): SQL Data Cleaning & Exploratory Analysis

Analysis of a global dataset of tech layoffs, covering company, industry,
location, and funding details. The raw data was messy — duplicate rows,
inconsistent text formatting, and a lot of missing values — so this project
first builds a clean, reliable table in SQL, then uses that table to answer
practical questions about who was laid off, where, and when.

## Dataset

~2,360 layoff events reported between 2020 and 2023, with these fields:
`company`, `location`, `industry`, `total_laid_off`, `percentage_laid_off`,
`date`, `stage`, `country`, `funds_raised_millions`.

## Tools

MySQL — window functions (`ROW_NUMBER`, `DENSE_RANK`), CTEs, self-joins,
aggregate functions, and string/date manipulation. Power BI for the
dashboard.

## Project structure

| File | Description |
|---|---|
| [`1_data_cleaning.sql`](./1_data_cleaning.sql) | Deduplicates, standardizes text/date formats, fixes null and blank values, and drops rows with no usable layoff data |
| [`2_exploratory_data_analysis.sql`](./2_exploratory_data_analysis.sql) | Aggregates the cleaned data to surface trends by company, industry, country, and time |
| `layoffs.csv` | Raw source data |

## Data cleaning process

1. **Removed duplicates** — copied the raw data into a staging table, then
   used `ROW_NUMBER()` partitioned across every column to flag and delete
   exact duplicate rows (rather than deleting straight from the raw table).
2. **Standardized formatting** — trimmed stray whitespace from company
   names, collapsed inconsistent labels like `Crypto`, `Crypto Currency`,
   and `CryptoCurrency` into a single `Crypto` industry, stripped a trailing
   `.` from `United States.` entries, and converted the `date` column from
   text to a proper `DATE` type.
3. **Handled missing data** — converted blank strings to real `NULL`s, then
   backfilled missing `industry` values using a self-join (if the same
   company had its industry listed in another row, that value was reused).
4. **Removed unusable rows** — dropped rows where both `total_laid_off`
   and `percentage_laid_off` were `NULL`, since they carried no signal for
   analysis, then dropped the helper column used for de-duplication.

## Dashboard

Working on building a Power BI dashboard on top of the cleaned data to visualize layoffs
by year, industry, company, and country.

## Key findings

- **The layoff wave grew every year through 2022, then started cooling.**
  Total layoffs by year: **2020 → ~81K, 2021 → ~15.8K, 2022 → ~161K,
  2023 → ~126K** (2023 figures only cover part of the year in this dataset).
- **Big Tech drove the largest single-company cuts.** Amazon had the
  highest cumulative layoffs of any company (~18K across multiple rounds),
  followed by Google (12,000 in a single event — the largest single layoff
  in the dataset), Meta (11,000), Salesforce, and Microsoft (~10,000 each).
- **Consumer, Retail, and Transportation were hit hardest by volume**,
  each losing well over 30,000 jobs total — more than "pure tech" sectors
  like Hardware or Infrastructure.
- **The U.S. dominates the dataset**, accounting for roughly 256K of total
  layoffs — more than 6x the next-highest country, India (~36K).
- **Some well-funded startups shut down entirely.** Several companies with
  100% workforce layoffs had raised significant capital beforehand —
  Britishvolt ($2.4B raised), Quibi ($1.8B), and Deliveroo Australia
  ($1.7B) among them — a reminder that funding raised is not a predictor
  of survival.
- **Year-by-year "most impacted" companies shifted with the market
  narrative**: Uber and Booking.com led 2020 (COVID-era travel collapse),
  Bytedance led 2021, and by 2022–2023 the largest cuts came from Meta,
  Amazon, Google, and Microsoft as Big Tech corrected post-pandemic
  over-hiring.
- **The cumulative (rolling) monthly total** shows layoffs were relatively
  gradual through 2021, then accelerated sharply starting in Q4 2022 —
  visible directly in the rolling-total query.

## How to run

1. Load `layoffs.csv` into a MySQL table named `layoffs`.
2. Run `1_data_cleaning.sql` top to bottom to produce the cleaned
   `layoffs_staging2` table.
3. Run `2_exploratory_data_analysis.sql` against that table to reproduce
   the analysis above.

---
*Base dataset originally sourced from a public tech-layoffs tracker.*
