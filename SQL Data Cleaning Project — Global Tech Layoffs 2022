SQL Data Cleaning Project — Global Tech Layoffs 2022

Portfolio Project #1


Project Overview
This project demonstrates a structured, production-style SQL data cleaning pipeline applied to a real-world dataset of global tech company layoffs. The goal was not just to clean the data — but to build a repeatable, documented process that mirrors what Data Engineers do inside bank pipelines every day.
Raw data is messy. Banks deal with this at petabyte scale. This project is my first step in learning to handle it properly.

Dataset
PropertyDetailSourceKaggle — Layoffs 2022Records1,600+ rowsColumnscompany, location, industry, total_laid_off, percentage_laid_off, date, stage, country, funds_raised_millionsIssues foundDuplicates, inconsistent naming, NULL values, wrong date format, trailing whitespace

Tools Used

MySQL — database engine
MySQL Workbench — query interface
SQL techniques — CTEs, Window Functions, STR_TO_DATE(), TRIM(), UPDATE with JOIN


Cleaning Process — Step by Step
Step 1 — Create a Staging Table
Never touch the raw data. Best practice is to duplicate the original table into a staging table and work from there.
sqlCREATE TABLE layoffs_staging LIKE layoffs;
INSERT INTO layoffs_staging SELECT * FROM layoffs;
Why this matters: In a bank pipeline, raw data in a Data Lake is immutable. You always transform into a new layer — this is exactly that pattern.

Step 2 — Remove Duplicates
The dataset has no unique ID column, so duplicates are detected using ROW_NUMBER() partitioned across all meaningful columns.
sqlWITH duplicate_cte AS (
  SELECT *,
    ROW_NUMBER() OVER (
      PARTITION BY company, location, industry,
      total_laid_off, percentage_laid_off, date,
      stage, country, funds_raised_millions
    ) AS row_num
  FROM layoffs_staging
)
DELETE FROM layoffs_staging2 WHERE row_num > 1;
Technique used: Window functions + CTE — key skills in the Nedbank DE job spec.

Step 3 — Standardise Data
Fixed inconsistent values across multiple columns:
sql-- Remove trailing whitespace
UPDATE layoffs_staging2
SET company = TRIM(company);

-- Consolidate 'Crypto', 'Crypto Currency', 'CryptoCurrency' → 'Crypto'
UPDATE layoffs_staging2
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';

-- Fix 'United States.' → 'United States'
UPDATE layoffs_staging2
SET country = TRIM(TRAILING '.' FROM country)
WHERE country LIKE 'United States%';

Step 4 — Fix Date Format
Date column was stored as TEXT. Converted to proper DATE type for time-series analysis.
sqlUPDATE layoffs_staging2
SET date = STR_TO_DATE(date, '%m/%d/%Y');

ALTER TABLE layoffs_staging2
MODIFY COLUMN date DATE;

Step 5 — Handle NULL Values
Populated missing industry values by self-joining on company name where another row had the same company with a known industry.
sqlUPDATE layoffs_staging2 t1
JOIN layoffs_staging2 t2
  ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL
AND t2.industry IS NOT NULL;

Step 6 — Remove Unusable Rows & Columns
Dropped rows where both total_laid_off and percentage_laid_off were NULL (no useful data), and removed the temporary row_num column.
sqlDELETE FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

ALTER TABLE layoffs_staging2 DROP COLUMN row_num;

What the Clean Data Enables
Once cleaned, this dataset is ready for:

Exploratory Data Analysis (EDA)
Loading into Azure Data Lake as a curated layer
Building a Power BI dashboard showing layoff trends by industry, country, and company stage
Feeding into ML models for workforce trend prediction


Key Learnings
- ConceptApplied?Staging table pattern (raw → clean)
- Window functions (ROW_NUMBER + PARTITION BY)
- CTEs for readable, modular SQL
- NULL handling strategies
- Data type conversion
- Self-JOIN for data imputation

Follow my Journey Linkedin: www.linkedin.com/in/tariq-mahomed-6316671a6

Files
FileDescriptionPortfolio Project - Data Cleaning.sql Full SQL cleaning script
README.md - This documentation
