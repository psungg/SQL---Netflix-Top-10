## SQL---Netflix-Top-10

![netflix_header]([netflix_header.png](https://github.com/psungg/SQL---Netflix-Top-10/blob/main/Images/netflix_header.png)

### Preview Dataset: all_week_countries

```
SELECT *
FROM all_weeks_countries
ORDER by cumulative_weeks_in_top_10 ASC, 
	weekly_rank ASC, week ASC, 
	country_name ASC;
```

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

