**Fixed Effects**: 
	- You estimate a coefficient for each level of the factor. 
	- Uses more degrees of freedom.
	- Appropriate when you're interested in the specific levels, testing a specific, distinct hypothesis about each level.  We're interested in the differences in means.
**Random Effects**: 
	- You assume the levels are a random sample from a larger population and are not of specific interest. Instead, you estimate a single **variance component** that represents how much the levels vary from the overall mean.
	- You care about whether the levels introduce a non-trivial amount of variability in the data.


## Fixed Effects
The fixed effects model estimates a separate intercept (mean) for each level:
$$ y_{ij} = \beta_0 + \beta_i \text{Factor}_{ij} + \epsilon_{ij} $$
Where:
- $y_{ij}$ is the outcome for observation $j$ in level $i$ of the factor.
- $\beta_0$ is the overall intercept (mean effect).
- $\beta_i$ is the coefficient (mean effect) for each specific level $i$ of the factor.
- $\epsilon_{ij}$ is the residual error, assumed to be normally distributed with variance $\Sigma^2$.

**Why Use Fixed Effects?**
1. Fixed effects are appropriate when you want to estimate the effect of each individual level. For example, you might want to know the specific mean test score for each school rather than just the overall variability between schools.
	- Good for situations where the levels of the factors are exhaustive or nearly exhaustive (e.g., comparing specific treatments, schools, or regions) where each level represents a distinct and meaningful entity.
2. Fixed effects do not assume that levels are randomly sampled from a larger population. Instead, each level is treated as a unique entity.
3. By estimating separate coefficients, fixed effects control for level-specific differences that could confound your results.

**When to Use Fixed Effects**
- You want to test specific hypotheses about individual levels, such as the difference between the mean test scores of each school.
- The levels are fixed and of intrinsic interest, such as analyzing only the schools in a specific district or region.
- You’re not interested in generalizing findings to a broader population outside of the specific levels included in the study.

**Summary**
Fixed effects models estimate a separate coefficient for each level of a factor, making them suitable when you’re interested in understanding or comparing specific groups within your data. This approach uses more degrees of freedom since it involves estimating individual means for each level, but it allows for precise testing of differences between those specific levels.


## Random effects:
We assume the level effects follow a normal distribution with mean 0 and variance $\sigma^2_u$:
$$y_{ij} = \beta_0 + u_i + \epsilon_{ij}$$
Where:
-$\beta_0$ is the overall intercept (mean effect),
-$u_i$ is the random effect for level$i$, assumed to be normally distributed with variance $\sigma^2_u$,
-$\epsilon_{ij}$ is the residual error, assumed to be normally distributed with variance $\sigma^2$.

**Why Estimate Variance Components?**
1. **Reduced Complexity**: Instead of estimating individual effects for each level, we estimate the **variance** of the random effects $\sigma^2_u$.
2. **Generalization**: Random effects models allow you to generalize results beyond the specific levels in your data. 
3. **Hierarchical/Nested Data**: Useful when you have data structured at multiple levels (e.g., students nested within schools).

**When to Use Random Effects**
- You’re not interested in individual group effects but in the overall variability across groups.
- There are many levels, and estimating a coefficient for each would consume too many degrees of freedom.
- You're dealing with hierarchical or nested data, where groups (like classrooms or subjects) introduce non-independence between observations.

The primary outcome statistic of these models are the variance components, the variance attributable to each random effect (e.g., between-patient variance, between-run variance).  We can view these as a proportion of the total variability in the outcome variable with the **ICC**, Intraclass Correlation Coefficient.  Although this metric indicates how strongly units in the same group resemble each other, we see from its formula $\text{ICC} = \frac{\sigma_\text{group}^2}{\sigma_\text{group}^2 + \sigma_\text{residual}^2}$ the variance attributed to the grouping factor and the residual variance.  An ICC of 1 indicates the variability is purely due to the difference between those groups, and 0 indicates it explains no variability in the outcome.  ICC gives an indication of the degree of clustering in the data.  This does *not* speak to quality of the model; we need something like AIC or LR tests for that.

**R Examples**
We specify random effects via the `1|` in front of the variables, indicating we want random intercepts for each level.
```r
library(lme4)
clinic.lmer <- lmer(cholesterol ~ 1 + (1|run) + (1|patient) + (1|run:patient), 
					data=clinic)
summary(clinic.lmer)
# optionally, jtools::summ(clinic.lmer) for more detail
```

**Summary**
Random effects allow you to model the variability between levels of a factor by estimating a single variance component, $\sigma^2_u$, rather than a separate coefficient for each level. This is particularly useful for hierarchical data and when you want to generalize findings to a broader population.

**Example R**
