## SQL - Netflix-Top-10

![netflix_header](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/netflix_header.png)

# Netflix Top 10: Analyzing Weekly Chart-Toppers

This dataset comprises Netflix's weekly top 10 lists for the most-watched TV shows and films worldwide. The data spans from June 28, 2021, to August 27, 2023.

This workspace is pre-loaded with two CSV files. 
- `netflix_top10.csv` contains columns such as `show_title`, `category`, `weekly_rank`, and several view metrics.
- `netflix_top10_country.csv` has information about a show or film's performance by country, contained in the columns `cumulative_weeks_in_top_10` and `weekly_rank`.

We've added some guiding questions for analyzing this exciting dataset! Feel free to make this workspace yours by adding and removing cells, or editing any of the existing cells. 

[Source: Netflix](https://www.netflix.com/tudum/top10/united-states?week=2023-08-27) 

### Preview Dataset: netflix top 10

```
SELECT *
FROM 'netflix_top10_country.csv'
ORDER BY 
	country_name ASC, cumulative_weeks_in_top_10 DESC;
```

![Fig01](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig01.png)

## Preview Datasets: netflix top 10 country

```
SELECT *
FROM all_weeks_global
ORDER by week ASC, 
cumulative_weeks_in_top_10 ASC, 
weekly_rank ASC;
```

![Fig02](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig02.png)

## Shows with most cumulative weeks in top 10

```
SELECT show_title, MAX(cumulative_weeks_in_top_10) as cumulative_weeks_in_top_10
FROM 'netflix_top10_country.csv'
GROUP BY show_title
ORDER BY max_cumulative_weeks_in_top_10 DESC;
```

### Most Popular Category - Total hours viewed by category

```
SELECT category, SUM(weekly_hours_viewed) AS total_hours_views
FROM 'netflix_top10.csv'
GROUP BY category
ORDER BY total_hours_views DESC;
```

![Fig03](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig03.png)

## Top 10 shows globally

```
SELECT 
	show_title,
	season_title,
	SUM(weekly_hours_viewed) AS total_hours_views
FROM 'netflix_top10.csv'
GROUP BY show_title, season_title
ORDER BY total_hours_views DESC;
```

![Fig04](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig04.png)

## Top 10 most viewed films globally

```
SELECT 
	show_title, 
	MAX(cumulative_weeks_in_top_10) AS cumulative_weeks_in_top_10,
	SUM(weekly_hours_viewed) AS total_hours_views
FROM 'netflix_top10.csv'
WHERE category LIKE '%Films%'
GROUP BY show_title
ORDER BY total_hours_views DESC;
```

![Fig05](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig05.png)

## Top 10 most viewed TV shows globally

```
SELECT 
	show_title,
	MAX(cumulative_weeks_in_top_10) AS cumulative_weeks_in_top_10,
	SUM(weekly_hours_viewed) AS total_hours_views
FROM 'netflix_top10.csv'
WHERE category LIKE '%TV%'
GROUP BY show_title
ORDER BY total_hours_views DESC;
```

![Fig06](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig06.png)

## Top 10 total cumulative weeks in top 10

```
SELECT 
	show_title, 
	MAX(cumulative_weeks_in_top_10) as max_cumulative_weeks_in_top_10
FROM 'netflix_top10.csv'
GROUP BY show_title
ORDER BY max_cumulative_weeks_in_top_10 DESC
LIMIT 10;
```

![Fig07](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig07.png)

## Highest Rank shows in Thailand

```
WITH RankedFilms AS (
  SELECT
    country_iso2,
    CASE
        WHEN season_title = 'N/A' THEN show_title
            ELSE season_title
    END AS content_title,
    MAX(cumulative_weeks_in_top_10) AS total_weeks_in_top_10,
	MIN(weekly_rank) AS highest_rank,
    ROW_NUMBER() OVER (PARTITION BY country_iso2 ORDER BY SUM(cumulative_weeks_in_top_10) DESC) as rank
  FROM 'netflix_top10_country.csv'
  WHERE country_iso2 = 'TH'
  GROUP BY country_iso2, content_title
)
SELECT country_iso2, content_title, highest_rank, total_weeks_in_top_10
FROM RankedFilms
ORDER BY country_iso2 ASC, rank ASC
LIMIT 
	10;
```

![Fig08](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig08.png)

## Top films in Thailand

```
WITH RankedFilms AS (
  SELECT
    country_iso2,
    CASE
        WHEN season_title = 'N/A' THEN show_title
            ELSE season_title
    END AS content_title,
    MAX(cumulative_weeks_in_top_10) AS total_weeks_in_top_10,
	MIN(weekly_rank) AS highest_rank,
    ROW_NUMBER() OVER (PARTITION BY country_iso2 ORDER BY SUM(cumulative_weeks_in_top_10) DESC) as rank
  FROM 'netflix_top10_country.csv'
  WHERE category LIKE '%Films%' AND country_iso2 = 'TH' --select category and country--
  GROUP BY country_iso2, content_title
)
SELECT country_iso2, content_title, highest_rank, total_weeks_in_top_10
FROM RankedFilms
WHERE rank <= 10
ORDER BY country_iso2 ASC, rank ASC;
```

![Fig09](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig09.png)

## Top TV shows in Thailand

```
WITH RankedFilms AS (
  SELECT
    country_iso2,
    CASE
        WHEN season_title = 'N/A' THEN show_title
            ELSE season_title
    END AS content_title,
    MAX(cumulative_weeks_in_top_10) AS total_weeks_in_top_10,
	MIN(weekly_rank) AS highest_rank,
    ROW_NUMBER() OVER (PARTITION BY country_iso2 ORDER BY SUM(cumulative_weeks_in_top_10) DESC) as rank
  FROM 'netflix_top10_country.csv'
  WHERE category LIKE '%TV%' AND country_iso2 = 'TH' --select category and country--
  GROUP BY country_iso2, content_title
)
SELECT country_iso2, content_title, highest_rank, total_weeks_in_top_10
FROM RankedFilms
WHERE rank <= 10
ORDER BY country_iso2 ASC, rank ASC;
```

![Fig10](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig10.png)
