## Statistical tests

#### Wald test
The Wald test is used to test whether a parameter is significantly different from a hypothesized value, often 0.
- Useful for large samples and when the normal approximation holds.
- Not useful for small sample sizes or extreme imbalances in the data.
- Fails when the estimated proportion is at or near 0 or 1, leading to a zero standard error and an undefined test statistic.
- **Note**: The Wald test is less reliable than the score test in most cases, especially with small samples or extreme proportions.

```r
# Wald test calculation
pi_hat <- x / n
SE <- sqrt(pi_hat * (1 - pi_hat) / n)
z_wald <- (pi_hat - p0) / SE
p_value <- 2 * pnorm(abs(z_wald), lower.tail = FALSE)
```

**Wald Confidence Interval**
The Wald confidence interval is calculated using the estimated proportion and its standard error.
- Useful for large samples where the normal approximation is reasonable.
- Not reliable for small samples or proportions near 0 or 1, as the interval may not make sense (e.g., [0, 0]).

```r
# Wald confidence interval
ci_wald_lower <- pi_hat - qnorm(0.975) * SE
ci_wald_upper <- pi_hat + qnorm(0.975) * SE
ci_wald <- c(ci_wald_lower, ci_wald_upper)
```

#### Score test
The score test (or Lagrange multiplier test) is an alternative to the Wald test, using the null standard error in the calculation of the test statistic.
- Useful when Wald test fails, especially for smaller samples.
- Provides a more reliable p-value than the Wald test in cases of extreme imbalance.
- Produces a confidence interval using the score statistic, more appropriate for small samples.

```r
# Score test calculation
SE_null <- sqrt(p0 * (1 - p0) / n)
z_score <- (pi_hat - p0) / SE_null
p_value_score <- 2 * pnorm(abs(z_score), lower.tail = FALSE)
```

**Score Confidence Interval**
The score confidence interval provides a more reliable estimate than the Wald interval, particularly with small sample sizes.

```
# Score confidence interval using Wilson method
ci_score <- binom.confint(x, n, conf.level = 0.95, methods = "wilson")
```
