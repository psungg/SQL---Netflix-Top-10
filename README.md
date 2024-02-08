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
FROM 'netflix_top10.csv'
ORDER BY 
	week ASC, weekly_hours_viewed DESC;
```

![Fig01](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/Fig01.png)

## Preview Datasets: all_week_global

```
SELECT *
FROM all_weeks_global
ORDER by week ASC, 
cumulative_weeks_in_top_10 ASC, 
weekly_rank ASC;
```

### Most Popular Category

```
SELECT category, SUM(weekly_hours_viewed) as total_hour_viewed
FROM public.all_weeks_global
GROUP BY category
ORDER BY total_hour_viewed DESC;
```

### Top 10 Most View Show Globally Across All Weeks

```
SELECT show_title, category, SUM(weekly_hours_viewed) as total_hours_viewed
FROM public.all_weeks_global
GROUP BY show_title, category
ORDER BY total_hours_viewed DESC
LIMIT 10;
```

