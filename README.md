# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/najirh/netflix_sql_project/blob/main/logo.png)

## 📊 Overview

This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. This README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

---

## 🎯 Objectives

- Analyze the distribution of content types (Movies vs TV Shows).
- Identify the most common ratings for Movies and TV Shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

---

## 🗃️ Dataset

- **Source:** [Kaggle - Netflix Movies and TV Shows](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

---

## 🧱 Schema

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


### 1. Count the Number of Movies vs TV Shows

```sql
SELECT type, COUNT(*) AS total_content
FROM [Netflix].[dbo].[netflix]
GROUP BY type;
```

**Objective:** Determine the distribution of content types on Netflix.


### 2. Find the Most Common Rating for Movies and TV Shows

```sql
WITH RATING_COUNT AS (
  SELECT  type, rating, COUNT(*) AS rate_count
FROM [Netflix].[dbo].[netflix]
GROUP BY type, rating
)
,
RANKING_RATING_COUNT AS (
SELECT type, rating, rate_count,
RANK () OVER (PARTITION BY type ORDER BY rate_count DESC) AS rnk
FROM RATING_COUNT
)

SELECT type, rating, rate_count
FROM RANKING_RATING_COUNT
WHERE rnk = 1;
```

**Objective:** Identify the most frequently occurring rating for each type of content.



### 3. List All Movies Released in a Specific Year (e.g., 2020)

```sql
SELECT title, release_year
FROM [Netflix].[dbo].[netflix]
WHERE release_year = 2020;
```

**Objective:** Retrieve all movies released in a specific year.

### 4. Find the Top 5 Countries with the Most Content on Netflix

```sql
 SELECT TOP(5) country, count (show_id) AS content_count
 FROM [Netflix].[dbo].[netflix]
 GROUP BY country	
 ORDER BY content_count DESC;
```

**Objective:** Identify the top 5 countries with the highest number of content items.

### 5. Identify the Longest Movie

```sql
SELECT MAX(duration) AS longest_movie
FROM [Netflix].[dbo].[netflix]
WHERE type = 'Movie';
```

**Objective:** Find the movie with the longest duration.

### 6. Find Content Added in the Last 5 Years

```sql
WITH YEAR_DIFFERENCE AS ( 
SELECT title, type, release_year, date_added, DATEDIFF(YEAR, date_added, GETDATE())YEAR_DIFF
FROM [Netflix].[dbo].[netflix]
)

SELECT title, type, release_year, date_added
FROM YEAR_DIFFERENCE
WHERE YEAR_DIFF = 5;
```

**Objective:** Retrieve content added to Netflix in the last 5 years.

### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

```sql
SELECT director, title	
FROM [Netflix].[dbo].[netflix]
WHERE director = 'Rajiv Chilaka'
```

**Objective:** List all content directed by 'Rajiv Chilaka'.

### 8. List All TV Shows with More Than 5 Seasons

```sql
WITH NUMBER_SEASON AS ( 
SELECT *, LEFT(duration, CHARINDEX(' ', duration) - 1) AS first_value
FROM [Netflix].[dbo].[netflix]
WHERE type = 'TV Show'
)

SELECT * FROM NUMBER_SEASON
WHERE first_value = 5 ;
```

**Objective:** Identify TV shows with more than 5 seasons.

### 9. Count the Number of Content Items in Each Genre

```sql
SELECT listed_in , COUNT(*) AS total_content
FROM [Netflix].[dbo].[netflix]
GROUP BY listed_in
ORDER BY total_content DESC;
```

**Objective:** Count the number of content items in each genre.

### 10.Find each year and the average numbers of content release in India on netflix. 
return top 5 year with highest avg content release!

```sql
SELECT TOP (5) YEAR(date_added) AS Year, COUNT(*) total_content_peryear, 
       ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM [Netflix].[dbo].[netflix] WHERE country = 'India'),2) AS total_content_india_percentage
FROM [Netflix].[dbo].[netflix]
WHERE country = 'India'
GROUP BY YEAR(date_added)
ORDER BY total_content_india_percentage DESC;
```


## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.
