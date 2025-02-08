## Implementation in R
```r
anova(simpler_model, model_of_interest)
```
The null hypothesis is that is that the more complex model does not provide a statistically significant improvement, so a lower p-value indicates better fit over the reduced model.

#### Calculating Pure Error ANOVA statistics by hand
In this situation, we have replicated observations for the same level of the independent variable `Day`.  This allows us to separate the total error into pure error and lack of fit.  If we were to do ANOVA with nested models (vs. a linear model in this case), we can only compare model fits but not know whether the chosen model is actually adequate.

Here we calculate Pure Error ANOVA statistics by hand:
```r
algae.quadratic <- lm(Algdensity ~ Day + I(Day^2), data=algae)
# summary(algae.quadratic)

algae.anova <- lm(Algdensity ~ as.factor(Day), data=algae)
# summary(algae.anova)


# From summaries
SSE_quadratic <- sum(residuals(algae.quadratic)^2)
SSE_pure_error <- sum(residuals(algae.anova)^2)
dfE <- algae.quadratic$df.residual
dfPE <- algae.anova$df.residual

# Pure error
cat("\n\nPure error")
cat("\ndfPE:", dfPE)
cat("\nSSPE:", SSE_pure_error)
MSPE <- SSE_pure_error / dfPE
cat("\nMSPE:", MSPE)

# Lack of fit
cat("Lack of fit")
dfLOF <- dfE - dfPE
cat("\ndfLOF:", dfLOF)
SSLOF <- SSE_quadratic - SSE_pure_error
cat("\nSSLOF:", SSLOF)
MSLOF <- SSLOF / dfLOF
cat("\nMSLOF:", MSLOF)
F_value <- MSLOF / MSPE
cat("\nF-value:", F_value)
F_crit <- qf(0.95, dfLOF, dfPE)
cat("\nF crit:", F_crit)
p_value <- pf(F_value, dfLOF, dfPE, lower.tail = FALSE)
cat("\np-value:", p_value)

# Error
cat("\n\nError")
cat("\ndfE:", dfE)
cat("\nSSE:", SSE_quadratic)
```