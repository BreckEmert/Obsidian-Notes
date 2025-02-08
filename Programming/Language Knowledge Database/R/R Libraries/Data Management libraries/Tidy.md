
![[download.png]]
![[tidyr nested data.png]]

Create a new column to visualize if it's a weekday:
```
vic_elec_day_type <- vic_elec |>
	filter(year(Time) == 2014) |>
	mutate(Day_Type = case_when(
		Holiday ~ "Holiday", 
		wday(Date) %in% 2:6 ~ "Weekday", 
		TRUE ~ "Weekend"))
```
![[TimeSeries-Scatterplots-weekdayfilter.png]]