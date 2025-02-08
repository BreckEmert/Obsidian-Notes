
## `tsibble` objects
A `tsibble` allows storage and manipulation of multiple time series in R.  It contains:
- An index: time information about the observation
- Measured variable(s): numbers of interest
- Key variable(s): optional unique identifiers for each series
It works with `tidyverse` functions.

**Examples (loaded along with the fpp3 object by default)**:
This object tells us that there are 15,150 rows and 6 columns, are separated by 1 year, and there are 263 different time-series (unique countries, the `Key`).  The time variable `Year` is the `Index`, the varying object `Country` is the `Key`, and everything else are `Measured variables`, the y variables.
![[TimeSeries-FPP3-Object.png]]

This data set has three `Keys` and only one `Measure`, and varies by `1Q` at each time.
![[TimeSeries-FPP3-Object2.png]]

#### Making a `tsibble`
```
mydata <- tsibble(
	year = 2015:2019, 
	y = c(123 ,39, 78, 52, 110), 
	index = year
)
```
![[Pasted image 20240930181237.png]]

You can pipe this object to achieve the same results, note that `|>` does the same thing as `%>%` *in this case (and most others)*:
```
mydata <- tibble(
	year = 2015:2019, 
	y = c(123, 39, 78, 52, 100)
)   |>
	as_tsibble(index = year)
```
If data isn't being read as the correct type, we can again pipe it to fix it.  This code corrects the `Index` `Month` from being `chr` to a `mth`:
```
z |>
	mutate(Month = yearmonth(Month)) |> 
	as_tsibble(index = Month)
```

Common time index variables can be created with these functions:

| Quarterly | yearquarter()    |
| --------- | ---------------- |
| Monthly   | yearmonth()      |
| Weekly    | yearweek()       |
| Daily     | as_date(), ymd() |
| Sub-daily | as_datetime()    |
`ymd` can be moved to anything such as `mdy`

## Example: prison population
Here we load in the data and verify its accuracy.  We notice the date column could be more specific to our analysis and be converted into quarters:
![[TimeSeries-Example-csv.png]]
So we tack on a `mutate` (still a `tibble`), remove the `date` columns and lastly turn it into a `tsibble` object:
```
prison <- readr::read_csv("data/prison_population.csv") |> 
	mutate(Quarter = yearquarter(date)) |> 
	select(-date) |> 
	as_tsibble(
		index = Quarter, 
		key = c(state, gender, legal, indigenous)
	)
```
![[TimeSeries-FPP3-Example-fixedcsv.png]]
