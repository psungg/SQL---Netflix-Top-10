## SQL---Netflix-Top-10

### Preview Dataset: all_week_countries

```
SELECT *
FROM all_weeks_countries
ORDER by cumulative_weeks_in_top_10 ASC, 
	weekly_rank ASC, week ASC, 
	country_name ASC;
```
