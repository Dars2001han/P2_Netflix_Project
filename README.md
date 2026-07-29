# P2_Netflix_Project
End-to-end Netflix SQL analytics project using PostgreSQL, showcasing data exploration, business intelligence, aggregate functions, window functions, string functions, date functions, and advanced SQL techniques.

# Netflix SQL Data Analysis

A comprehensive SQL data analysis project using the **Netflix Movies & TV Shows Dataset**. This project demonstrates PostgreSQL skills by solving real-world business problems using SQL queries, window functions, string functions, aggregate functions, and date functions.

---
<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/7/75/Netflix_icon.svg" alt="Netflix Logo" width="120">
</p>

<h1 align="center">Netflix SQL Data Analysis Project</h1>

<p align="center">
  <b>Analyze Netflix Movies & TV Shows using PostgreSQL to uncover valuable business insights through SQL.</b>
</p>

<p align="center">

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-025E8C?style=for-the-badge&logo=database&logoColor=white)
![Data Analysis](https://img.shields.io/badge/Data%20Analysis-FF6F00?style=for-the-badge) 

![Netflix Dataset](https://img.shields.io/badge/Dataset-Netflix-E50914?style=for-the-badge&logo=netflix&logoColor=white)

</p>

---
# Dataset link 

Dataset link to download through from kaggle 
-[Dataset Link](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

# Project Overview

Netflix is one of the world's leading streaming platforms, offering thousands of movies and TV shows across various genres and countries. Every day, millions of users watch content, generating valuable data that can be analyzed to understand viewing trends, content distribution, and business performance.

This project uses **PostgreSQL** to analyze the Netflix Movies and TV Shows dataset by solving real-world business problems using SQL. The analysis focuses on exploring content types, ratings, genres, release trends, countries, directors, actors, and other key insights through efficient SQL queries.

The project demonstrates practical SQL techniques commonly used by Data Analysts, Business Analysts, and Data Engineers for data exploration and business intelligence reporting.

---

# Project Objectives

The primary objectives of this project are to:

- Analyze Netflix's Movies and TV Shows dataset using SQL.
- Perform exploratory data analysis (EDA) to understand the dataset.
- Answer real-world business questions using SQL queries.
- Practice PostgreSQL functions including aggregate, window, string, and date functions.
- Extract meaningful insights from content distribution and user-related information.
- Demonstrate advanced SQL concepts through practical business scenarios.
- Build a portfolio-ready SQL project for Data Analyst interviews.

---

# Key Features

- Database Creation
- Table Creation
- Data Exploration
- Business Problem Solving
- Aggregate Functions
- Window Functions
- Common Table Expressions (CTEs)
- String Functions
- Date Functions
- Ranking Functions
- Case Statements
- Subqueries
- Business Insights Generation

---

---

# Project Overview

Netflix is one of the world's largest streaming platforms with thousands of movies and TV shows from different countries and genres.

The objective of this project is to analyze the Netflix dataset using SQL to answer business questions related to:

- Content Distribution
- Ratings Analysis
- Genre Analysis
- Country-wise Content
- Director & Actor Analysis
- Release Trends
- Content Classification

This project demonstrates how SQL can be used for exploratory data analysis (EDA) and business intelligence.

---

# Repository Structure

```
Netflix-SQL-Project/
│
├── netflix_project.sql          # SQL Queries
├── netflix_titles.csv           # Netflix Dataset
└── README.md
```

---

# Dataset Information

The dataset contains Netflix Movies and TV Shows with the following columns.

| Column | Description |
|----------|-------------|
| show_id | Unique Content ID |
| type | Movie or TV Show |
| title | Title of the Content |
| director | Director Name |
| casts | Cast Members |
| country | Country of Production |
| date_added | Date Added to Netflix |
| release_year | Original Release Year |
| rating | Content Rating |
| duration | Movie Duration / TV Seasons |
| listed_in | Genres |
| description | Content Description |

---

# Technologies Used

- PostgreSQL
- SQL
- CSV Dataset

---

# Database Creation

```sql
CREATE DATABASE netflix_project;
```

---

# Table Creation

```sql
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

---

# Data Exploration

## Total Content

```sql
SELECT COUNT(*) AS total_content
FROM netflix;
```

---

## Distinct Content Types

```sql
SELECT DISTINCT type
FROM netflix;
```

---

# Business Problems & SQL Solutions

## 1. Count the Number of Movies vs TV Shows

```sql
SELECT
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
```

---

## 2. Find the Most Common Rating for Movies and TV Shows

```sql
SELECT
    type,
    rating
FROM
(
    SELECT
        type,
        rating,
        COUNT(*),
        RANK() OVER(
            PARTITION BY type
            ORDER BY COUNT(*) DESC
        ) AS ranking
    FROM netflix
    GROUP BY 1,2
) AS t1
WHERE ranking = 1;
```

---

## 3. List All Movies Released in 2020

```sql
SELECT *
FROM netflix
WHERE type = 'Movie'
AND release_year = 2020;
```

---

## 4. Find the Top 5 Countries with the Most Netflix Content

```sql
SELECT
    UNNEST(STRING_TO_ARRAY(country, ',')),
    COUNT(*) AS most_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

---

## 5. Identify the Longest Movie

```sql
SELECT *
FROM netflix
WHERE type = 'Movie'
AND duration =
(
    SELECT MAX(duration)
    FROM netflix
);
```

---

## 6. Find Content Added During the Last Five Years

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added,'Month DD, YYYY')
>= CURRENT_DATE - INTERVAL '5 years';
```

---

## 7. Find All Movies and TV Shows Directed by Rajiv Chilaka

```sql
SELECT *
FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';
```

---

## 8. List All TV Shows Having More Than Five Seasons

```sql
SELECT *
FROM netflix
WHERE type='TV Show'
AND SPLIT_PART(duration,' ',1)::INT > 5;
```

---

## 9. Count Content Available in Each Genre

```sql
SELECT
    UNNEST(STRING_TO_ARRAY(listed_in,',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```

---

## 10. Find the Top Five Years with the Highest Percentage of Indian Content Releases

```sql
SELECT
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (
            SELECT COUNT(show_id)
            FROM netflix
            WHERE country='India'
        )::numeric *100,
        2
    ) AS avg_release
FROM netflix
WHERE country='India'
GROUP BY country,release_year
ORDER BY avg_release DESC
LIMIT 5;
```

---

## 11. List All Documentary Movies

```sql
SELECT *
FROM netflix
WHERE type='Movie'
AND listed_in ILIKE '%documentaries';
```

---

## 12. Find Content Without a Director

```sql
SELECT *
FROM netflix
WHERE director IS NULL;
```

---

## 13. Find Salman Khan Movies Released During the Last 10 Years

```sql
SELECT *
FROM netflix
WHERE casts ILIKE '%Salman Khan%'
AND release_year >
EXTRACT(YEAR FROM CURRENT_DATE)-10;
```

---

## 14. Find the Top 10 Actors Appearing in Indian Netflix Content

```sql
SELECT
    UNNEST(STRING_TO_ARRAY(casts,',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country='India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
```

---

## 15. Categorize Content as Good or Bad Based on Description

```sql
SELECT
    category,
    COUNT(*) AS content_count
FROM
(
    SELECT
        CASE
            WHEN description ILIKE '%kill%'
              OR description ILIKE '%violence%'
            THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
```

---

# SQL Concepts Demonstrated

- Database Creation
- Table Creation
- SELECT Statement
- WHERE Clause
- DISTINCT
- Aggregate Functions
  - COUNT()
  - MAX()
  - ROUND()
- GROUP BY
- ORDER BY
- LIMIT
- Subqueries
- CASE Statement
- Window Functions
  - RANK()
  - OVER()
  - PARTITION BY
- Date Functions
  - CURRENT_DATE
  - TO_DATE()
  - EXTRACT()
- String Functions
  - STRING_TO_ARRAY()
  - UNNEST()
  - SPLIT_PART()
- Type Casting
- ILIKE Operator

---

# Business Insights Generated

- Movie vs TV Show Distribution
- Rating Analysis
- Country-wise Content Distribution
- Longest Movie Identification
- Recent Content Additions
- Director-wise Content Analysis
- TV Shows by Number of Seasons
- Genre-wise Content Distribution
- India Release Trend Analysis
- Documentary Movies
- Missing Director Information
- Actor Analysis
- Content Classification using Keywords

---

# Learning Outcomes

This project demonstrates practical knowledge of:

- SQL Query Writing
- PostgreSQL
- Exploratory Data Analysis (EDA)
- Business Intelligence Reporting
- Window Functions
- String Manipulation
- Date Functions
- Subqueries
- Data Transformation
- Real-world SQL Problem Solving

---

# Future Improvements

- Create SQL Views
- Develop Stored Procedures
- Add Indexes for Query Optimization
- Build an Interactive Power BI Dashboard
- Build a Tableau Dashboard
- Create Automated Reports

---

# Author

**Darshan D S**

Assistant Professor | Data Analytics Enthusiast

### Skills

- SQL
- PostgreSQL
- Power BI
- Tableau
- Python
- Machine Learning
- Data Analytics

---

## If you found this project helpful, consider giving this repository a ⭐.
