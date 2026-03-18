-- Data Cleaning

-- The data was imported via csv with its raw formating. This will allow me to showcase my data cleaning skills. 
-- This is a simple dataset for layoffs around the world from 2022-2023


SELECT *
FROM layoffs;

-- 1. Remove Duplicates
-- 2. Standardize the Data
-- 3. Null Values or Blank Values
-- 4. Remove any Columns 


-- I created a new table in the event I make a mistake and need to go back to the raw data. 

CREATE TABLE layoffs_staging
LIKE layoffs;

SELECT *
FROM layoffs_staging;

INSERT layoffs_staging
SELECT *
FROM layoffs;



WITH duplicate_cte AS
(
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions) AS row_num
FROM layoffs_staging
)
SELECT *
FROM duplicate_cte
WHERE row_num >1;

-- Ada is an example of a duplicate shown below
SELECT *
FROM layoffs_staging
WHERE company = 'Ada';


-- I create a new table called layoff_staging2. This will show a new table without duplicates after run the DELETE statement. 
CREATE TABLE `layoffs_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;


SELECT *
FROM layoffs_staging2
WHERE row_num > 1;

INSERT INTO layoffs_staging2
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions) AS row_num
FROM layoffs_staging; 

-- I could not run the DELETE statement. I had to temporarily disable safe updates as shown below. 
DELETE
FROM layoffs_staging2
WHERE row_num > 1;

SET SQL_SAFE_UPDATES = 0;

-- All duplicate were removed
SELECT *
FROM layoffs_staging2

-- Standardizing data

SELECT company, TRIM(company)
FROM layoffs_staging2

UPDATE layoffs_staging2
SET company = TRIM(company);

SELECT DISTINCT industry
FROM layoffs_staging2;

UPDATE layoffs_staging2
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';

-- Someone inserted a period at the end of United States. 
SELECT DISTINCT country
FROM layoffs_staging2
WHERE country LIKE 'United States%';

SELECT DISTINCT country, TRIM(TRAILING '.' FROM country)
FROM layoffs_staging2
ORDER BY 1

UPDATE layoffs_staging2
SET country = TRIM(TRAILING '.' FROM country)
WHERE country LIKE 'United States%';

-- Change date to standard date format then change the format from text to date.
SELECT `date`
FROM layoffs_staging2

SELECT `date`,
str_to_date(`date`,'%m/%d/%Y')
FROM layoffs_staging2

UPDATE layoffs_staging2
SET `date` = str_to_date(`date`,'%m/%d/%Y')

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;

SELECT * 
FROM layoffs_staging2

-- Remove Null & Blank Values

SELECT *
FROM layoffs_staging2
WHERE total_laid_off IS NULL
WHERE percentage_laid_off IS NULL;


SELECT *
FROM layoffs_staging2
WHERE industry IS NULL
OR industry = '';

-- Update Industry to show Travel and not a blank
SELECT *
FROM layoffs_staging2
WHERE company = 'Airbnb';

SELECT *
FROM layoffs_staging2 t1
JOIN layoffs_staging2 t2
	ON t1.company = t2.company
WHERE (t1.industry IS NULL OR t1.industry = '')
AND t2.industry IS NOT NULL

-- NOTE that I had to change the blanks to NULL in order for the UPDATE to work. 
UPDATE layoffs_staging2
SET industry = NULL
WHERE industry = '';

UPDATE layoffs_staging2
SET industry = 'Travel' WHERE industry IS NULL;

-- REMOVE Columns and Rows, We wont need them as they show now layoffs at all

SELECT *
FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

DELETE
FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

SELECT *
FROM layoffs_staging2

-- I deleted the row_num column as it was only needed to delete duplicates
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
