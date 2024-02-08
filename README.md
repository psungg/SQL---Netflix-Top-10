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

## Top 10 Lists in a Single Weekly Top 10 List Spanning All Categories

```
SELECT week, show_title, SUM(weekly_hours_viewed) AS total_weekly_hours_viewed
FROM 'netflix_top10.csv'
GROUP BY week, show_title
ORDER BY week DESC, total_weekly_hours_viewed DESC;
```

![Fig11](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig11.png)

## Top 10 shows by month

```
WITH MonthlyAggregates AS (
    SELECT
        DATE_TRUNC('month', CAST(week AS DATE)) AS month,
        CASE
        WHEN season_title = 'N/A' THEN show_title
            ELSE season_title
    END AS content_title,
        SUM(weekly_hours_viewed) AS total_monthly_hours_viewed
    FROM
        'netflix_top10.csv'
    GROUP BY
        DATE_TRUNC('month', CAST(week AS DATE)),
        content_title
),
RankedShows AS (
    SELECT
        month,
        content_title,
        total_monthly_hours_viewed,
        RANK() OVER (PARTITION BY month ORDER BY total_monthly_hours_viewed DESC) AS rank
    FROM
        MonthlyAggregates
)
SELECT
    month,
    content_title,
    total_monthly_hours_viewed
FROM
    RankedShows
WHERE
    rank <= 10 AND month LIKE '%%' -- Filter YY-MM
ORDER BY
    month DESC,
    rank ASC;
```

![Fig12](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig12.png)

## Category hours viewed by month

```
SELECT
    DATE_TRUNC('month', CAST(week AS DATE)) AS month,
    category,
    SUM(weekly_hours_viewed) AS total_monthly_hours_viewed
FROM
    'netflix_top10.csv'
WHERE
	month LIKE '%23-06-%' -- Filter YY-MM
GROUP BY
    DATE_TRUNC('month', CAST(week AS DATE)),
    category
ORDER BY
    month DESC,
    total_monthly_hours_viewed DESC;
```

![Fig13](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig13.png)

### Top 10 Shows achieved Top 1 the most

```
WITH MonthlyAggregates AS (
    SELECT
        DATE_TRUNC('month', CAST(week AS DATE)) AS month,
        CASE
            WHEN season_title = 'N/A' OR season_title IS NULL THEN show_title
            ELSE season_title
        END AS content_title,
        SUM(weekly_hours_viewed) AS total_monthly_hours_viewed
    FROM
        'netflix_top10.csv'
    GROUP BY
        month,
        content_title
),
RankedShows AS (
    SELECT
        month,
        content_title,
        total_monthly_hours_viewed,
        RANK() OVER (PARTITION BY month ORDER BY total_monthly_hours_viewed DESC) AS rank
    FROM
        MonthlyAggregates
),
TopRankedShows AS (
    SELECT
        content_title,
        COUNT(*) AS times_rank_1
    FROM
        RankedShows
	WHERE
    rank <= 1 AND month LIKE '%%' -- Filter YY-MM
    GROUP BY
        content_title
)
SELECT
    content_title,
    times_rank_1
FROM
    TopRankedShows
ORDER BY
    times_rank_1 DESC
LIMIT 
	10;
```

![Fig14](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig14.png)

## Top 10 rank 1 shows in 2022

```
WITH MonthlyAggregates AS (
    SELECT
        DATE_TRUNC('month', CAST(week AS DATE)) AS month,
        CASE
            WHEN season_title = 'N/A' OR season_title IS NULL THEN show_title
            ELSE season_title
        END AS content_title,
        SUM(weekly_hours_viewed) AS total_monthly_hours_viewed
    FROM
        'netflix_top10.csv'
    GROUP BY
        month,
        content_title
),
RankedShows AS (
    SELECT
        month,
        content_title,
        total_monthly_hours_viewed,
        RANK() OVER (PARTITION BY month ORDER BY total_monthly_hours_viewed DESC) AS rank
    FROM
        MonthlyAggregates
),
TopRankedShows AS (
    SELECT
        content_title,
        COUNT(*) AS times_rank_1
    FROM
        RankedShows
	WHERE
    rank <= 1 AND month LIKE '%22-%' -- Filter YY-MM
    GROUP BY
        content_title
)
SELECT
    content_title,
    times_rank_1
FROM
    TopRankedShows
ORDER BY
    times_rank_1 DESC
LIMIT 
	10;
```

![Fig15](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig15.png)

## Top 10 rank 1 TV shows in 2022

```
WITH MonthlyAggregates AS (
    SELECT
        DATE_TRUNC('month', CAST(week AS DATE)) AS month,
        CASE
            WHEN season_title = 'N/A' OR season_title IS NULL THEN show_title
            ELSE season_title
        END AS content_title,
        SUM(weekly_hours_viewed) AS total_monthly_hours_viewed
    FROM
        'netflix_top10.csv'
	WHERE
		category LIKE '%TV%'
    GROUP BY
        month,
        content_title
),
RankedShows AS (
    SELECT
        month,
        content_title,
        total_monthly_hours_viewed,
        RANK() OVER (PARTITION BY month ORDER BY total_monthly_hours_viewed DESC) AS rank
    FROM
        MonthlyAggregates
),
TopRankedShows AS (
    SELECT
        content_title,
        COUNT(*) AS times_rank_1
    FROM
        RankedShows
	WHERE
    rank <= 1 AND month LIKE '%%' -- Filter YY-MM
    GROUP BY
        content_title
)
SELECT
    content_title,
    times_rank_1
FROM
    TopRankedShows
ORDER BY
    times_rank_1 DESC
LIMIT 
	10;

```

![Fig16](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig16.png)

## Top 10 Films in Thailand 2022

```
WITH MonthlyAggregates AS (
    SELECT
        DATE_TRUNC('month', CAST(week AS DATE)) AS month,
        CASE
            WHEN season_title = 'N/A' OR season_title IS NULL THEN show_title
            ELSE season_title
        END AS content_title,
        MAX(cumulative_weeks_in_top_10) AS cumulative_weeks_in_top_10
    FROM
        'netflix_top10_country.csv'
	WHERE
		country_name = 'Thailand' AND category LIKE '%Films%'
    GROUP BY
        month,
        content_title
),
RankedShows AS (
    SELECT
        month,
        content_title,
        cumulative_weeks_in_top_10,
        RANK() OVER (PARTITION BY month ORDER BY cumulative_weeks_in_top_10 DESC) AS rank
    FROM
        MonthlyAggregates
),
TopRankedShows AS (
    SELECT
        content_title,
        COUNT(*) AS times_rank_1
    FROM
        RankedShows
	WHERE
    rank <= 1 AND month LIKE '%22-%' -- Filter YY-MM
    GROUP BY
        content_title
)
SELECT
    content_title,
    times_rank_1
FROM
    TopRankedShows
ORDER BY
    times_rank_1 DESC
LIMIT 
	10;
```

![Fig17](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig17.png)
