https://otexts.com/fpp3
## Time series patterns
- **Trend**: Long-term increase or decrease in the data.  Does not have to be linear.
- **Seasonal**: Influence by seasonal factors (quarter/month of the year, day of the week)
- **Cyclic**: Data exhibits rises and falls that are not of fixed period (often a business cycle, usually of at least two years)
*Cyclic and seasonal patterns are quite different*

![[TimeSeries-PatternsExamples.png]]
1. The monthly housing sales (top left) show strong seasonality within each year, as well as some strong cyclic behavior with a period of about 6–10 years. There is no apparent trend in the data over this period.
2. The US treasury bill contracts (top right) show results from the Chicago market for 100 consecutive trading days in 1981. Here there is no seasonality, but an obvious downward trend. Possibly, if we had a much longer series, we would see that this downward trend is actually part of a long cycle, but when viewed over only 100 days it appears to be a trend.
3. The Australian quarterly electricity production (bottom left) shows a strong increasing trend, with strong seasonality. There is no evidence of any cyclic behavior here.
4. The daily change in the Google closing stock price (bottom right) has no trend, seasonality or cyclic behavior. There are random fluctuations which do not appear to be very predictable, and no strong patterns that would help with developing a forecasting model.

#### Seasonal
***Note: It is normal to see a spike in March because it has more days***

```
aus_retail |>  # Loaded by default with fpp3 package
  index_by(Month) |>
  summarize(Avg_Turnover = mean(Turnover, na.rm=TRUE)) |>
  autoplot(Avg_Turnover)
```
![[TimeSeries-SeasonalBase.png]]
```
aus_retail |>
  index_by(Month) |>
  summarize(Avg_Turnover = mean(Turnover, na.rm=TRUE)) |>
  gg_season(Avg_Turnover)
```
![[TimeSeries-SeasonalPlot.png]]

Change the period with `gg_season(data, period="week").
Plot each month individually with `gg_subseries()` instead.  This might allow us to spot things like this, showing that the average negative trend is only due to Q4:
![[TimeSeries-Subseries-TrendDueToQ4.png]]

Use `facet_wrap` to put the states into rows:
```
holidays |> gg_season(Trips) + 
	face_wrap(vars(State), nrow=2, scale="free_y") + 
	labs(y="thousands of trips", title="Australian domestic holiday nights")
```
![[TimeSeries-Seasonal-FacetWrap.png]]
This shows that the location of the states changes the seasonal trends of the data.

## White noise data
![[acf graph.png]]
Sampling distribution of $r_k$ for white noise data is asymptotically $N(0, 1/T)$.
- 95% of all $r_k$ for white noise must lie within $\pm1.96/\sqrt{T}$.
- If this is not the case, the series is probably not WN.
- Common to plot lines at $\pm1.96/\sqrt{T}$ when plotting ACF.  These are the *critical values*.