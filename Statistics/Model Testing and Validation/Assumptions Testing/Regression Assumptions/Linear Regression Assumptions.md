For more R implementations, see [[Performance - Model Assumptions]]
## Normality of residuals
The null hypothesis of the Shapiro-Wilk test for the normality of residuals is that the residuals are normally distributed, so a lower p-value indicates a higher degree of evidence against normality.

Base R has a Shapiro-Wilk test and a plotting option.
```r
shapiro.test
qqnorm(residuals(model))
qqline(residuals(model))
```
Confidence intervals can be added with `ggpubr` paired with `ggplot2`:
```r
ggpubr::ggqqplot(residuals(model), conf.int=TRUE)
```
Highlight the outliers:
```r
ggplot(data.frame(residuals = residuals(model)), aes(sample = residuals)) +
  stat_qq() +
  stat_qq_line() +
  geom_point(aes(
    color = abs(residuals) > threshold_value)) +  # Adjustable threshold
  theme_minimal()
  
```

Alternatives to the Shapiro-Wilk test for normality of residuals are the Anderson-Darling and Kolmogorov-Smirnov.  The AD is more sensitive to deviations in the tails of the distribution, and the KS can be used to test against any specified continuous distribution.