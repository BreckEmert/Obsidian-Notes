Contrasts refer to specific linear combinations of treatment means that allow us to make comparisons between treatments or groups.  They isolate and test particular hypothesis about the differences between treatment groups, rather than general tests for *any* difference like ANOVA.

Contrasts are typically used when you have one categorical outcome variable.  The contrasts are applied to the treatment groups (factor levels) of the categorical independent variable(s) to compare the effects on a single outcome.

#### What contrasts are best at:
1. **Pre-specified comparisons**: Contrasts are statistically powerful when you have a specific hypothesis about how (certain) groups should differ.  Instead of performing a broad, exploratory test across all groups (like ANOVA), you can create contrasts to compare specific subsets or patterns in the data.
2. **Controlling error rates**: Because contrasts allow for independent comparisons (orthogonal contrasts), they help avoid the problem of inflated error rates that can occur when making multiple comparisons.

#### Example:
- **Question**: Is treatment A different from the average of treatments B, C, and D?
- **Contrast**: $1*A, -0.33*B, -0.33*C, -0.33*D$
	- We're combining information from **multiple groups** into a **single comparison**.
- **Why it works best here**: We're only testing one difference.

**Checking orthogonality:**
```r
# Contrast matrix
contrast_matrix <- matrix(c(3, 0, 3, -2, -2, -2, 
                            1, 0, -1, 0, -1, 1, 
                            0, 4, 0, -4, 0, 0, 
                            0, 0, 0, 2, -1, -1), 
                          nrow = 4, byrow = TRUE)

# Orthogonality function
check_orthogonality <- function(contrast1, contrast2) {
  return(sum(contrast1 * contrast2) == 0)
}

# Check orthogonality
orthogonality_results <- matrix(NA, nrow = 6, ncol = 1)

orthogonality_results[1] <- check_orthogonality(
  contrast_matrix[1, ], contrast_matrix[2, ])  # 1 and 2
orthogonality_results[2] <- check_orthogonality(
  contrast_matrix[1, ], contrast_matrix[3, ])  # 1 and 3
orthogonality_results[3] <- check_orthogonality(
  contrast_matrix[1, ], contrast_matrix[4, ])  # 1 and 4
orthogonality_results[4] <- check_orthogonality(
  contrast_matrix[2, ], contrast_matrix[3, ])  # 2 and 3
orthogonality_results[5] <- check_orthogonality(
  contrast_matrix[2, ], contrast_matrix[4, ])  # 2 and 4
orthogonality_results[6] <- check_orthogonality(
  contrast_matrix[3, ], contrast_matrix[4, ])  # 3 and 4

orthogonality_results
```
**Example with a contrast matrix:**
```r
# Load data
bacteria <- read.delim("Data/bacteria.txt", header = TRUE)
bacteria$package <- factor(bacteria$package, 
                           levels = c("Commercial", "Vacuum", "Mixed Gas", "CO2"))

# ANOVA
model <- aov(logcount ~ package, data = bacteria)

# Contrasts
contrast_matrix <- rbind(
  "c1" = c(1, -1/3, -1/3, -1/3),  # c1 = T1 - (T2 + T3 + T4) / 3
  "c2" = c(0, 1, -1/2, -1/2),     # c2 = T2 - (T3 + T4) / 2
  "c3" = c(0, 0, 1, -1)           # c3 = T3 - T4
)

contrast_estimates <- glht(model, linfct = mcp(package = contrast_matrix))
summary(contrast_estimates)

# 95% CIs
confint(contrast_estimates, level = 0.95)
confint(contrast_estimates, level = 0.95, 
        calpha = qnorm(1 - 0.05 / (2 * 3)))  # Bonferroni
        
```
**Example using Scheffe's method for all possible contrasts, not just pairwise like Tukey's method:**
```r
library(DescTools)

steel.aov_machine <- aov(y ~ machine, data = steel)

steel.scheffe <- ScheffeTest(steel.aov_machine)

print(steel.scheffe)
```


## Paper notes: Optimal contrast analysis with heterogeneous variances and budget concerns
https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6435144/
#### Contrasts with Heterogeneous Variances:
When variances across groups are not equal (heteroscedasticity), traditional contrast tests like ANOVA can lead to inaccurate results. In these cases, the **Welch-Satterthwaite procedure** is recommended as an alternative. This approach adjusts the degrees of freedom to better account for unequal variances and provides more accurate results for contrasts when variances differ across groups.

The Welch-Satterthwaite test can be applied both for:
- **Standardized contrasts** (useful when comparing multiple studies with different scales)
- **Unstandardized contrasts** (more intuitive when the units of measurement are consistent across groups)

#### Incorporating Cost and Power Considerations:
In practical settings, researchers often face budget constraints or need to maximize statistical power within a fixed cost. This paper introduces optimization methods that:
- **Minimize sample size while maintaining desired power levels**: Use nonlinear optimization to determine the smallest number of subjects needed for a study while still achieving sufficient power.
- **Maximize power within a fixed cost**: For scenarios with limited budgets, sample size can be allocated optimally to achieve the highest possible power under cost constraints.

The paper also provides algorithms for computing optimal power and sample size allocations for these contrast analyses.

#### Key Takeaways:
1. **Heterogeneous variances** require the use of adjusted degrees of freedom for accurate contrast analysis, which can be accomplished using the Welch-Satterthwaite method.
2. **Sample size and cost efficiency** are critical in study design, and optimizing sample sizes can ensure sufficient power while minimizing unnecessary cost or resource use.

#### Example with Cost Minimization:
```
# Simulate cost-minimization for Welch-Satterthwaite test
optimize_sample_size <- function(power, cost, variances, coefficients) {
  # Example optimization logic based on paper's recommendations
  # This would use nonlinear optimization methods in practice
  return(list(optimal_sample_size = c(100, 120, 80), 
              total_cost = 15000))
}

# Example use:
power_goal <- 0.80
total_cost <- 20000
group_variances <- c(15, 30, 20)
contrast_coefficients <- c(1, -0.5, -0.5)

optimal_allocation <- optimize_sample_size(power_goal, total_cost, group_variances, contrast_coefficients)
print(optimal_allocation)
```

