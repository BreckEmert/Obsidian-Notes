## Assumptions
- **Linearity**: The relationship between the predictors and the response is linear.
	- The expected value of the response variable is a linear function of the predictor variables.
	- **GLM**: Assumes a linear relationship between the predictors and the **transformed** response (transformed through the link function).
- **Independence**: The residuals are independent of each other.
	- There is no correlation among the residuals (errors are not autocorrelated).
	- **GLM**: In cases like logistic or Poisson regression, additional checks for overdispersion or clustered data may be needed.
- **Homoscedasticity**: The variance of the residuals is constant across all levels of the predictors.
	- The residuals have equal spread (no pattern or funnel shape in residual plots).
	- **GLM**: Does not assume homoscedasticity. The variance of the response is allowed to change with the mean.
- **Normality of Residuals**: The residuals are normally distributed.
	- The error terms follow a normal distribution (bell-shaped curve).
	- **GLM**: Does not assume normality of residuals. Instead, the response distribution is specified by the family, which determines the distribution of the errors.
- **No Multicollinearity**: The predictor variables are not highly correlated with each other.
	- There is no strong linear relationship among the independent variables.
	- **GLM**: Multicollinearity can still be an issue, but it affects the stability of parameter estimates rather than the model assumptions directly.
- **Additivity**: Assumes an additive effect of predictors on the response.
	- **GLM**: Assumes additivity on the scale of the link function (e.g., additive on the log-odds scale for logistic regression).


## ANOVA Table Structure
Interpreting a basic model comparison ANOVA output in R:
![[QuadraticModelBasicANOVA.png]]
- (multiple) $R^2 = \frac{\text{SS}_\text{Regression}}{\text{SS}_\text{Total}}$ represents the proportion of variance explained by the model.
- (adjusted) $R^2 = 1-(\frac{\text{SSE}/\text{df}_\text{Error}}{\text{SST}/\text{df}_\text{Total}})$ adjusts $R^2$ for the number of predictors.
- $\text{RSE} = \sqrt\frac{SSE}{n-p}$
- "on $25 \: \text{df}$" means $\text{dfE}$, calculated as $n-p$.
- "F-statistic on 2 and 25 $\text{df}$" is "on $\text{df}_{\text{Regression}} = p - 1$ and $\text{df}_{\text{Error}} = n - p$ $\text{df}$".
- p-value calculated using `pf(1080, 2, 25, lower.tail=FALSE)`

#### Components of a model comparison ANOVA:
- **Degrees of Freedom**
	- $\text{df}_{\text{Regression}}$: 1 for simple linear regression because $\beta_0$ does not contribute towards explaining variation in $y$.
	- $\text{df}_{\text{Error}}$: Also called residual degrees of freedom, calculated as $n - p$.
	- $\text{df}_{\text{Total}}$: $n - 1$, representing the total degrees of freedom for the dataset.
- **Sum of Squares**
	- $\text{SS}_{\text{Regression}} = \sum_{i=1}^{n} (\hat{y}_i - \bar{y})^2$: Represents the explained variance.
	- $\text{SS}_{\text{Error}} = \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$: The sum of squares of the residuals, representing the unexplained variance.
	- $\text{SS}_{\text{Total}}$: The total sum of squares, calculated as $\text{SS}_{\text{Regression}} + \text{SS}_{\text{Error}}$.
- **Mean Squares**
	- $\text{MS}_{\text{Regression}}$: The mean square for regression, calculated as $\frac{\text{SS}_{\text{Regression}}}{\text{df}_{\text{Regression}}}$.
	- $\text{MS}_{\text{Error}}$: The mean square for error, calculated as $\frac{\text{SS}_{\text{Error}}}{\text{df}_{\text{Error}}}$.
- **F-statistic (F*)***
	- $F = \frac{\text{MS}_{\text{Regression}}}{\text{MS}_{\text{Error}}}$, used to test whether the model explains a significant portion of the variance in comparison to an intercept-only model (null model).
	- So, we want this to be high, and therefore higher than the critical F.
- **p-value**
	- Calculated from the F-distribution with `pf(F, dfR, dfE, lower.tail=FALSE)`.
- Critical F ($F_\text{crit}$) (not in ANOVA)
	- Calculated with `qf(1-alpha, dfR, dfE)`

#### Using `lower.tail` in R:
**`lower.tail = TRUE`**:
- Returns the **lower tail probability** (left side of the distribution).
- Use when testing if the observed statistic is **smaller** than expected (e.g., left-tailed t-tests).
**`lower.tail = FALSE`**:
- Returns the **upper tail probability** (right side of the distribution).
- Use when testing if the observed statistic is **larger** than expected (e.g., F-tests, right-tailed t-tests, chi-square tests).
- Most common in hypothesis testing since large test statistics often indicate significance.

- F-tests (ANOVA, model comparisons): `lower.tail = FALSE`
- Right-tailed t-tests (mean > hypothesized value): `lower.tail = FALSE`
- Left-tailed t-tests (mean < hypothesized value): `lower.tail = TRUE`
- Two-tailed t-tests: Use `lower.tail = TRUE` or `lower.tail = FALSE` depending on direction.
- Chi-square tests (variance, goodness-of-fit): `lower.tail = FALSE`

#### Other kinds of ANOVA calculations
- Pure Error (PE) is the component of error variance that reflects natural variation among replicated observations for the same level of $X$.
	- Calculated when there are replicates of levels in $X$, allowing separation of error into Pure Error (PE) and Lack of Fit (LOF).
	- PE is the MSPE, obtained by summing the squared differences between each observation and its group mean then dividing by $\text{df}_\text{PE}$.
		- $\text{SSPE} = \sum_{i=1}^{m} \sum_{j=1}^{r_i} (y_{ij} - \bar{y}_i)^2$
		- $\text{MSPE} =  \text{SSPE} / \text{df}_{\text{PE}}$
		- $\text{df}_{\text{PE}} = \sum_{i=1}^{m} (r_i - 1)$
		- $\text{df}_{\text{LOF}} = \text{df}_{\text{Residual}} - \text{df}_{\text{PE}}$
R calculation with replicates:
```r
algae.anova <- lm(Algdensity ~ as.factor(Day), data=algae)
SSE_PE <- sum(residuals(algae.anova)^2)
```
R calculation without replicates:


- Lack of Fit (LOF): Tests whether the chosen model fits as well as treating each level as a separate group.
	- Without replicates, LOF is calculated by comparing the fitted model to a saturated model where each level of $X$ is treated as a unique group, which has zero residual variance.
	- $\text{MSLOF} = \left( \text{SSE}_{\text{model}} - \text{SSE}_{\text{PE}} \right) / \, \text{df}_{\text{LOF}}$

## Model-Fitting Practice Questions

> [!question]- Test if the relationship between `AdvertisingSpend` and `Sales` differs by `Region`.   Continuous response `Sales`. Categorical `Region` and continuous `AdvertisingSpend`.
`lm(Sales ~ AdvertisingSpend*Region, data=dataset)`

> [!question]- Test if `Age` affects the likelihood of `CustomerChurn`.  Binary response `CustomerChurn`.  Continuous `Age`.
`glm(CustomerChurn ~ Age, family=binomial, data=dataset)`

> [!question]- Test if `BloodPressure` differs based on `Treatment` and `Gender`, controlling for `Age`.  Continuous response `BloodPressure`. Categorical `Treatment`, `Gender`, and continuous `Age`.
`lm(BloodPressure ~ Age + Treatment + Gender, data=dataset)`

> [!question]- Test if the average `TestScore` differs across `School`.  Continuous response `TestScore`. Categorical `School`
`aov(TestScore ~ School, data=dataset)`

> [!question]- Test if `HoursStudied` and `TutoringStatus` affect the probability of passing.  Binary response `PassFail`. Categorical `TutoringStatus` and continuous `HoursStudied`
`glm(PassFail ~ HoursStudied + TutoringStatus, family=binomial, data=dataset)`

> [!question]- Test if the effect of `FertilizerAmount` on `Yield` varies across different `Fields`.  Continuous response `Yield`. Random effects for `Field` and continuous `FertilizerAmount`
`lme4::lmer(Yield ~ FertilizerAmount + (1|Field), data=dataset)`

> [!question]- Test if `NumberOfPurchases` is associated with `PromotionType`.  Count response `NumberOfPurchases`. Categorical `PromotionType`
`glm(NumberOfPurchases ~ PromotionType, family=poisson, data=dataset)`

> [!question]- Test if `Diet` affects `Weight`, controlling for `InitialWeight`.  Continuous response `Weight`. Categorical `Diet` and continuous `InitialWeight`
`lm(Weight ~ Diet + InitialWeight, data=dataset)`

> [!question]- Test if the effect of `ExposureLevel` on `DiseaseStatus` differs by `Gender` and `SmokingStatus`.  Binary response `DiseaseStatus`. Categorical `Gender`, `SmokingStatus`, and continuous `ExposureLevel`
`glm(DiseaseStatus ~ ExposureLevel*Gender*SmokingStatus, family=binomial, data=dataset)`

> [!question]- Test if there is a quadratic relationship between `Year` and `Revenue`.  Continuous response `Revenue`. Continuous `Year` (quadratic)
`lm(Revenue ~ Year + I(Year^2), data=dataset)`

> [!question]- Test if `PlantHeight` differs by `FertilizerType` and `WateringSchedule`, including interaction.  Continuous response `PlantHeight`. Categorical `FertilizerType`, `WateringSchedule`
`aov(PlantHeight ~ FertilizerType*WateringSchedule, data=dataset)`

> [!question]- Test if `CustomerSatisfactionScore` varies across different `StoreLocations`.  Continuous response `CustomerSatisfactionScore`. Random effects for `StoreLocation`
`library(lme4)
lmer(CustomerSatisfactionScore ~ 1 + (1|StoreLocation), data=dataset)`

> [!question]- Test if there is a significant lack of fit in the linear relationship between `CaffeineDose` and `ReactionTime`.  Continuous response `ReactionTime`. Continuous `CaffeineDose` with replicates at each dose level.
`model_linear <- lm(ReactionTime ~ CaffeineDose, data=dataset);
model_anova <- lm(ReactionTime ~ as.factor(CaffeineDose), data=dataset)`

#### Model-Fitting Metrics
Deviance: lower is better, it will go to zero as you implement the saturated model
AIC/BIC: lower is better
Mallows' $C_p$: a value close to $p+\text{intercept}$ suggests the model is unbiased.
- Measures the trade-off between bias and variance
- $C_p < p$: Likely high bias (underfitting)
- $C_p > p$: Likely high variance (overfitting)
LRT: a higher p-value indicates the simpler model fits better (fit less complex first in the formula)