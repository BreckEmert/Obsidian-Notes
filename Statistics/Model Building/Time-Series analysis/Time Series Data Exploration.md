Use `gg_lag` to visualize lagged trends.  Each graph shows $y_t$ plotted against $y_{t-k}$ for different values of $k$:
`new_production |> gg_lag(Beer, geom="point")
![[TimeSeries-Exploration-LagPlot.png]]
We spot negative and linear trends because we are plotting peaks against troughs if `lag=2` and peaks against peaks if `lag=4`:
![[TimeSeries-Exploration-LagCorrelations.png]]