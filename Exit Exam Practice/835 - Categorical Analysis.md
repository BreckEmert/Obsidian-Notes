## Contingency Tables
#### Odds ratio
$\text{OR} = \frac{a/c}{b/d} = \frac{ad}{bc}$
- $\text{OR} > 1$: Event more likely in the first group (row)
- $\text{OR} < 1$: Event more likely in the second group (row)

| 80  | 20  |
| --- | --- |
| 50  | 50  |
For Treatment A odds of success is $\frac{80}{20} = 4$
For Treatment B odds of success if $\frac{50}{50} = 1$
The odds ratio is then $\frac{4}{1} = 4$

**Odds ratio is not relative risk.**  Relative risk compares probabilities while odds ratios compares odds (the ratio of event to non-event).
- Relative risk is always closer to 1 than the odds ratio; odds ratio exaggerates the perceived effect when the event rate is low.
- For example, an OR of 2 doesn't mean there's twice the risk.  Relative risk might be closer to something like 1.5.
- If the odds ratio is 1, this means the odds of the event occurring in both groups are the same, indicating no association between the groups and the event.

Let's practice with a question.
![[ContingencyTable-ConditionalOR-MarginalOR-Questions.png]]
We first note how to put this into a single table:
![[ContingencyTables-Questions3WayTable.png]]
But it's easier to work with it in 2 2x2 tables:
![[ContingencyTables-Questions2WayTables.png]]
Because we're interested in defendants' associations, we know to split the ratios into defendants.  We make sure to only get defendant/victim combos of the same race, because otherwise we're confounding effects.
That is, to find the conditional odds ratio for
- a white defendant (a measure of their increase in odds of receiving the death penalty as a opposed to a black person), we take the
	- odds of a white on white death penalty $19/132$, divided by the
	- odds of a black on white death penalty $11/52$.  **This yields 0.68.**
- a black defendant, we take the
	- odds of a a black on black death penalty $6/97$ divided by the
	- odds of a white on black death penalty $0/9$.  **This yields 0.00**.
#### Relative risk
$\text{RR} = \frac{a/(a+b)}{c/(c+d)}$
$$
\text{Odds ratio} = \frac{\hat{\pi}_1/(1 - \hat{\pi}_1)}{\hat{\pi}_2/(1 - \hat{\pi}_2)} 
= \text{Relative risk} \times \left( \frac{1 - \hat{\pi}_2}{1 - \hat{\pi}_1} \right)
$$
#### Simpson's Paradox
The result that a marginal association can have a different direction from the conditional associations is called Simpson's paradox.  It happens with quantitative and categorical variables.  For example, quantitative variables $X$ and $Y$ can have a positive correlation, yet a negative partial correlation after adjusting for $Z$.

This figure shows why it happens.  For each defendant's race, the figure plots the proportion receiving the death penalty at each victims' race.  Each proportion is labeled by a letter symbol giving the victims' race category.  Surrounding each observation is a circle having area proportional to the number of observations at that combination of defendant's race and victims' race.  For instance, the $W$ in the largest circle represents a proportion of 0.113 receiving the death penalty for cases with white defendants and white victims.  I won't explain why you have to figure it out, otherwise it won't make sense.
![[SimpsonsParadox.png]]

## Chi-square tests of independence
The Chi-square test of independence is used to test whether there is a significant association between two categorical variables.  It is commonly applied to contingency tables.

The chi-square distribution describes the SS of independent standard normal random variables.  So the test is just asking if the assignment of a true case to either of the groups is equally likely, the count of differences follow some standard normal amount.  That is, we're testing if the distribution within each row is the same across the columns and if the distribution within each column is the same across the rows.

To perform one manually on a contingency table, we want to focus on the square difference between expected and actual frequencies.  Expected are the row totals divided by the grand total, and actual are the cells divided by the column totals.

|            | Male | Female | **Total** |
| ---------- | ---- | ------ | --------- |
| Democrat   | 300  | 400    | 700       |
| Republican | 150  | 150    | 300       |
| **Total**  | 450  | 550    | 1000      |
Here, the expected counts for Democrats are 700/1000 and for Republicans 300/1000.  We break these down by gender to get 450*.7 and 550*.7, and 150*.3 and 150*.3.  We find the squared difference between those and the actual cells, and then sum all of those to plug it into the chi-square test.  Of course, the $\text{df} = (r-1)(c-1)$.
#### Limitations
- Chi-square tests only test for association and don't tell us the strength or nature of the relationship.
- Chi-square tests require larger sample sizes and fail when the expected frequencies are small (any cell <5).  We use **Fisher's Exact Test** for smaller tables.
- The chi-square test is used for nominal data.  To gain power by testing an ordinal variable, we use something like the Cochran-Mantel-Haenszel (CMH) test.
	- To test when only one variable is ordinal, use cumulative logit models.
#### Fisher's Exact Test
For 2x2 tables, independence corresponds to an odds ratio of $\theta = 1$.  Fisher's exact test looks at all possible tables with the same row and column totals, and sums up the probabilities of all tables that are as or more extreme than the observed table under the null hypothesis.  As such, it doesn't mind if the expected counts are small (esp. <5).
- Can be extended to larger contingency tables but becomes less powerful.

## GLMs
The core of a GLM:
- A linear predictor $\eta = X\beta$ where $X$ represents the predictors (covariates) and $\beta$ represents the coefficients.
- A link function to connect the linear predictor to the mean of the distribution.
	- The link function transforms the outcome variable into a form that the linear predictor can model effectively.
- A random component $Y$ which follows a distribution from the exponential family (e.g., Normal, Binomial, Poisson).
#### Binary Data
The most common GLM for binary data is the logistic regression model, which uses a logit link function $\text{logit}(p) = \log(\frac{p}{1-p})$ where $p$ is the probability of success.  We interpret the coefficient by exponentiating it, where then it represents the change in odds in the outcome for a one-unit change in $x$.

If we fit a logistic regression model for whether a student passes a test (1 = pass, 0 = fail) based on hours studied, we interpret a positive regression coefficient for hours studied as an increase in the odds of passing the test for each additional hour studied.  $\exp(\beta)$ represents the multiplicative change in the odds of passing the test for each one-hour increase in study time.

**Alternatives:**
- Probit Regression:
	- Uses the inverse CDF of the standard normal distribution.
	- Assumes that the underlying latent variable is normally distributed.
	- The coefficient estimates relate to the probability of success through the normal distribution.
- Complementary Log-Log (Cloglog) Model:
	- $\log(-\log(1-p)) = X\beta$
	- Useful for modeling rare events where extreme values of the response are of interest.
	- Gives an asymmetric curve for the relationship between $X$ and $p$, unlike the symmetric S-shaped curve of logistic regression.
- Linear Probability Model (LPM):
	- No link function, we directly use the linear model.
	- Used for simplicity.
	- Coefficients represent changes in the probability of success for a one-unit change in the predictor.

#### Counts and Rates
The most common GLM for counts and rates is Poisson regression, where the canonical link function is the log link $\log(\mu) = X\beta$ where $\mu$ is the expected count.  Log-counts cannot be negative which keeps our model sensible, and it allows multiplicative effects on the linear scale.

**Alternatives**
- Negative binomial regression.
	- Used if the count data are overdispersed.  The Poisson distribution assumes the mean and the variance are equal, so overdispersion occurs when the variance exceeds the mean (even after controlling for the relevant variables).  Another way of saying this is if the observed variance is higher than its predicted value by the model.
	- Adds a dispersion parameter to model the extra variability.

## Model Checking
#### Model-wise Statistics
- Likelihood Ratio Test
	- Compares two similar models, testing if the added parameters significantly improves the model fit.
	- $\Lambda = -2(\log L_\text{reduced} - \log L_\text{full})$
```r
# Likelihood ratio test
Anova(fit, test="LRT")
```
- Deviance
	- Deviance is the likelihood-ratio statistic for a test of whether all parameters that are in the saturated model but not in the working model equal zero.
	- $\text{Deviance} = 2(\log L_\text{saturated} - \log L_\text{fitted})$
		- where $\log L$ is the log-likelihood of the respective model
	- A lower deviance indicates better model fit.  We only interpret relative deviances because it scales with $n$.  A residual deviance of 0 indicates the fitted model has a perfect fit.
- Residual Deviance
	- Measures the discrepancy between the observed data and fitted data vs the null model.
	- $\text{Residual Deviance} = -2\log L_\text{fitted}$
	- We can divide the statistic by the $\text{df}$ ($n-p$) to assess model fit - if the ratio is close to 1, the model is a good fit.  If the ratio is high, there's still a lot of unexplained variation.
		- A more formal way to test it is with a chi-square test with $\text{df} = n-p$.  If the p-value is small, the model does not fit well.  The formula for testing is $\text{p-value} = 1- \text{pchisq}(\text{Residual Deviance}, \text{df})$.
- Information Criterion
	- AIC (Akaike Information Criterion)
		- Same as residual deviance but adds a penalty term, the number of parameters, $2k$.
		- Lower AIC values indicate a better fit, and are again only useful relatively.
	- BIC (Bayesian Information Criterion)
		- Same as residual deviance but adds a penalty term, $k\log(n)$.
		- A stronger penalty than AIC, as with larger datasets it's easier to maintain find significance with more parameters.
- Multiple Correlation
	- A summary of predictive power which is the correlation $R$ between the observed responses $y_i$ and the model's fitted values $\hat{u}_i$.
	- $R^2$ then describes the proportion of the variation in $y$ that is explained by those predictors.
	- An advantage of $R$ compared to $R^2$ is that it uses the original scale and it has value approximately proportional to the effect size.
		- For instance, with a single quantitative explanatory variable, the correlation is the slope multiplied by the ratio of standard deviations of the two variables.
		- Sensitive to the range of values of the data.
- Pseudo-$R^2$
#### ROC Curve
ROC shows the sensitivity and the specificity of the predictions for all the possible cutoffs $\pi_0$.  It's more informative than a classification table because of this.
- Y-axis (Sensitivity or True Positive Rate): The proportion of actual positives correctly identified by the model.  $\text{Sensitivity} = \frac{\text{True Positives}}{\text{True Positives}+\text{False Negatives}}$
- **X-axis (1-Specificity or False Positive Rate):** The proportion of actual negatives that are incorrectly classified as positives.  $1 - \text{Specificity} = \frac{\text{False Positives}}{\text{True Negatives} + \text{False Positives}}$
- At $\pi_0 = 0$, all predictions are positive, so sensitivity=1 and specificity=0.
- At $\pi_0 = 1$, all predictions are negative, so sensitivity= 0 and specificity=1.
![[ROCcurve.png]]

**We integrate the ROC curve to get the AUC, where 1.0 is a perfect classifier and 0.5 is random.**  Closely related (the exact same as AUC in binary classification) is the **concordance index**, which is the probability that a classifier correctly ranks a randomly chosen positive instance higher than a randomly chosen negative instance.  To get it we simply count the number of correctly predicted pairs divided by all possible pairs.
#### Residuals
For a GLM with a binomial random component, the standardized residual compares $y_i$ to its fitted value $n_i \hat{\pi}_i$ and divides by the $\text{SE}$, $\text{Standardized residual} = (y_i - n_i \hat{\pi}_i)/\text{SE}$.  The standardized residual has an approximately normal distribution when the model holds, so an absolute value larger than roughly 2 or 3 provides evidence of a lack of fit.

- Deviance residuals
	- The contributions of each observation to the overall deviance
- Pearson residuals
	- Based on the difference between observed and expected values divided by the square root of the expected value.

Unlike standardized residuals, deviance residuals and Pearson residuals do not appropriately recognize parameter redundancies, so the textbook does not use them.

## Logistic Regression
Logistic regression models a binary response variable, and is important because 1) binary data are the most common form of categorical data and 2) the methods extend directly to nominal and ordinal response variables.  Three alternatives to this are briefly covered in the GLM section above.

For any given observation with a predictor value $x$, the logit (log-odds) of the success probability is modeled as a linear function of $x$:
$$\text{logit}[\pi(x)] = \log\left[\frac{\pi(x)}{1 - \pi(x)}\right] = \alpha + \beta x$$
We're saying that
- the logit (or log-odds), which is defined as the log of the ratio of the probability of success to the probability of failure, 
- can be predicted by our linear predictor $\alpha + \beta x$.

We often want to translate observations back to probabilities using our model since we ultimately care about the probability of success $\pi(x)$.  This takes individual observations and gets our predicted probability of positive for it:
$$\pi(x) = \frac{e^{\alpha + \beta x}}{1 + e^{\alpha + \beta x}}$$
#### Further diving into the terms
The first logistic regression formula mentioned above indicates that the logit increases by $\beta$ for every 1-unit increase in $x$.  We aren't good at thinking on a logit scale, though.

If we exponentiate both sides of the logistic equation, we obtain an interpretation that uses the odds and the odds ratio.  The odds of success are
$$\frac{\pi(x)}{1 - \pi(x)} = \exp(\alpha + \beta x) = e^{\alpha} \left(e^{\beta}\right)^x$$
Therefore, the odds multiply by $e^\beta$ for every 1-unit increase in $x$.  That is, the odds at level $x+1$ equal the odds at $x$ multiplied by $e^\beta$.  When $\beta = 0$, $e^\beta = 1$, and the odds do not change as $x$ changes.

**Simpler interpretation of Logistic Regression**
- The rate of change of the probability $\pi(x)$ varies with the value of $\pi(x)$, meaning it's fastest around 0.5 and slower near 0 or 1.
- The slope at any point is given by $\beta\pi(x)(1 - \pi(x))$, and the steepest slope occurs when $\pi(x) = 0.5$, with a slope of $0.28*\beta$.
- The median effective level is the value of $x$ where $\pi(x) = 0.5$, which occurs at $x = -\frac{\alpha}{\beta}$, which represents the point at which each outcome has a 50% chance.
![[LogisticRegressionCurve-LinearApproximation.png]]

## Statistical Inference for Logistic Regression
#### Wald Test (not used, we use LRtest)
The Wald test assesses the significance of individual predictors in the model by checking whether the corresponding coefficient ($\beta$) is significantly different from zero.  It's calculated as:
$$z = \frac{\hat{\beta}}{SE}$$
This statistic follows a standard normal distribution when the null hypothesis ($H_0: \beta = 0$) is true. If the $p$-value associated with the Wald statistic is small, we reject $H_0$ and conclude that the predictor has a significant effect on the response variable.
#### Confidence Intervals for Effects
A Wald confidence interval for the effect of a predictor in logistic regression is based on the estimated coefficient ($\hat{\beta}$) and its standard error ($SE$). The confidence interval is calculated as:
$$
\hat{\beta} \pm z_{\alpha/2} (SE)
$$
Exponentiating the endpoints yields a confidence interval for $e^{\beta}$, the multiplicative effect on the odds of a 1-unit increase in the predictor $x$.

If the probabilities are mainly near 0 or 1 (e.g., rare events), it's better to construct a **profile likelihood confidence interval** instead, as the Wald intervals may become inaccurate in these cases.

For example:
- $\hat{\beta} = 0.497$ with $\text{SE} = 0.102$, gives a 95% Wald confidence interval for $\beta$ as $0.497 \pm 1.96 \times 0.102 = (0.298, 0.697)$.
- The profile likelihood confidence interval, by contrast, is $(0.308, 0.709)$.

We can even find the CI for the linear predictor value $\hat{\alpha} + \hat{\beta} x$ and then applying the $\exp()/ [ 1+\exp()]$ transform to the endpoints.  Here's how to use `R` to find point and interval estimates at all observed values of the explanatory variables:
```r
Crabs <- read.table("http://www.stat.ufl.edu/~aa/cat/data/Crabs.dat", header=TRUE)
fit <- glm(y ~ width, family=binomial, data=Crabs)
pred.prob <- fitted(fit) # ML fitted value estimate of P(Y=1)
lp <- predict(fit, se.fit=TRUE) # linear predictor
LB <- lp$fit - 1.96 * lp$se.fit # confidence bounds for linear predictor
UB <- lp$fit + 1.96 * lp$se.fit # better: use qnorm(0.975) instead of 1.96
LB.p <- exp(LB) / (1 + exp(LB)) # confidence bounds for P(Y=1)
UB.p <- exp(UB) / (1 + exp(UB))
cbind(Crabs$width, pred.prob, LB.p, UB.p)
```

**Why not just use the sample proportions to estimate probabilities?**
Instead of estimating $P(Y = 1)$ using the logistic model fit, we could use the sample proportion to estimate it.  For example, if six crabs have sample width 26.5, and four of them have satellites, the sample proportion would be $\hat{\pi} = 4/6 = 0.67$, which is similar to the model-based estimate.

But when the logistic regression model holds, the model-based estimator is much better than the sample proportion.  It uses *all* the data rather than only the data at the particular $x$ value.  In the textbook example the model $\text{SE}$ is 0.04 while the $\text{SE}$ for the sample proportion with 6 observations is $\sqrt{\hat{\pi} (1 - \hat{\pi}) / n} = \sqrt{0.67 \times 0.33 / 6} = 0.19$.

## Logistic Regression with Categorical Predictors (factors)
#### Multiple binary predictors
$$\text{logit}[P(Y = 1)] = \alpha + \beta_1 x + \beta_2 z$$
![[LogisticModel-Logits-TwoBinaryFactors.png]]
#### Factors
A factor having only two categories requires only a single indicator variable, but a factor with $c$ categories requires $c-1$ indicator variables.
$$\text{logit}[P(Y = 1)] = \alpha + \beta^x_i + \beta^z_k$$
By itself, the parameter estimate for a single category of a factor is irrelevant.  Different ways of handling parameter redundancies result in different values for that estimate.  An estimate makes sense only by comparison with one for another category.  Exponentiating a *difference* between estimates for two categories determine the odds ratio relating to the effect of classification in one category rather than the other.
#### Tests of Conditional Independence and of Homogeneity for three-way contingency tables
In examples with two categorical predictors, $x$ identifies two groups to compare and $z$ is a control variable or a potential confounder.  For example, in a clinical trial $x$ might refer to two treatments and $z$ might refer to several centers that recruited the patients for the study.  The data can then be represented in several 2x2 contingency tables. 

## Multiple Logistic Regression
The parameter $\beta_j$ refers to the effect of $x_j$ on the log odds that $Y = 1$, adjusting for the other $x$'s.

Within the a specific factor's indicators, any one curve is another curve shifted to the right or left.  This is because they are all measured in reference to the reference category (termed "dummy coding").  Of course this does not hold true for interaction terms.
![[MultipleLogisticRegression-AllCurvesShifts.png]]

#### Standardized interpretations
For quantitative explanatory variables, comparing estimates is only relevant if those variables have the same units, so that a 1-unit change means the same thing for each.  Otherwise, we can use *standardized* coefficients.  We can obtain these by fitting the model to standardized explanatory variables, replacing each $x_j$ by $(x_j - \hat{x}_j)/s_{x_j}$, where $s_{x_j}$ denotes the sample SD of $x_j$.  A one-unit change in the standardized variable is a standard deviation change in the original variable.  Then, $\hat{\beta_j}$ represents the effect of a SD change in $x_j$, adjusting for the other variables.  The standardized effect estimate for $x_j$ is th unstandardized estimate multiplied by $s_{x_j}$.

For example, let's say width has $\bar{x} = 26.30$ and $s_x = 2.11$ while color has $\bar{c} = 2.44$ and $s_c = 0.80$.  If the model is $\text{logit}(\hat{\pi}) = -10.071 + 0.458x - 0.509c$, the standardized explanatory variables would have effects $(2.11)(0.458) = 0.97$ and $(0.80)(-0.509) = -0.41$.  A SD increase in width is estimated to have more than double the effect of a SD increase in color, adjusting for the other variable.

## Baseline-Category Logit Models for nominal responses
So far we've been working with continuous and binary responses, and now we cover dealing with multicategory responses.  We can simultaneously use all pairs of categories by specifying odds of the outcome in one category instead of another.  If we set category $c$ as the baseline, the *baseline-category logits* are
$$\log(\frac{\pi_j}{\pi_c}), j=1, \dots, c-1$$
For $c=3$, for instance, the model uses $\log(\pi_1/\pi_3)$ and $\log(\pi_2/\pi_3)$.  Conditional on the response falling in category $j$ or in category $c$, $\log(\pi_j/\pi_c)$ is the log odds that the response is $j$.
The baseline-category logit model with an explanatory variable $x$ is
$$
\hspace{5em} \log(\frac{\pi_j}{\pi_c}), \alpha_j + \beta_jx, j=1 \dots, c-1
\hspace{6em}(6.1)
$$
The model has $c-1$ equations, with separate parameters for each.  The effects vary according to the category paired with the baseline.  These equations determine equations for all pairs of categories.  When $c=3$, for example, 
$$
\begin{align}
\log\left(\frac{\pi_1}{\pi_2}\right)
	& = \log\left(\frac{\pi_1/\pi_3}{\pi_2/\pi_3}\right)
	= \log\left(\frac{\pi_1}{\pi_3}\right) - 
		\log\left(\frac{\pi_2}{\pi_3}\right) \\
& = (\alpha_1 + \beta_1 x) - (\alpha_2 + \beta_2 x) \\
& = (\alpha_1 - \alpha_2) + (\beta_1 - \beta_2) x \hspace{10em}(6.2)\\
\end{align}
$$

This equation has the form $\alpha + \beta x$ with intercept parameter $\alpha = (\alpha_1 - \alpha_2)$ and with slope parameter $\beta = (\beta_1 - \beta_2)$.  With $p$ explanatory variables, this extends to
$$
\log\left(\frac{\pi_j}{\pi_c}\right) = \alpha_j + \beta_{j1}x_1 + \beta_{j2}x_2 + \dots + \beta_{jp}x_p, \quad j = 1, \dots, c - 1
\hspace{1em}(6.3) \hspace{3em}
$$

Software for multicategory logit models fits all $(c-1)$ equations simultaneously using the Fisher scoring iterative algorithm.  The baseline category is arbitrary and the same ML parameter estimates occur for a pair of categories no matter which baseline category you use.

#### Example: What do alligators eat?
Table 6.1 comes from a study by the Florida Game and Fresh Water Fish Commission of the foods that alligators eat.  For 59 alligators captured in Lake George, Florida, Table 6.12 shows the primary food type (in volume) found in the alligator's stomach.  The primary food type has $c=3$ categories: Fish, Invertebrate, and Other.  The invertebrates were primarily apple snails, aquatic insects, and crayfish.  The *other* category includes amphibian, mammal, plant material, stones or other debris, and reptiles.  The table also shows the alligator length, which varies between 1.24 and 3.89 meters.
![[BaselineCategoryLogitModels-AlligatorExample.png]]

Let $Y$ = primary food choice and $x$ = alligator length.  Here is the `R` output for fitting baseline-category logit model with *other* as the baseline category:
```r
Gators <- read.table("http://www.stat.ufl.edu/~aa/cat/data/Alligators.dat", 
					 header=TRUE)
Gators
```
![[GatorsDatasetHead.png]]
```r
library(VGAM)  # package for multivariate GLMs, such as multinomial models
fit <- vglm(y ~ x, family=multinomial, data=Gators) # vglm = vector GLM
coef(fit, matrix = TRUE)
```
![[Gators-VGAM-coef.png]]
```r
summary(fit)
```
![[Alligators-VGAM-summary.png]]

The ML prediction equations for the two baseline-category logit models are
$$
\begin{align}
\log\left(\frac{\hat{\pi}_1}{\hat{\pi}_3}\right) = 1.618 - 0.110x \\
\log\left(\frac{\hat{\pi}_2}{\hat{\pi}_3}\right) = 5.697 - 2.465x
\end{align}
$$
By (6.2), the estimated log odds that the response is $fish$ (category 1) rather than *invertebrate* (category 2) equals
$$
\log\left(\frac{\hat{\pi}_1}{\hat{\pi}_2}\right) = (1.618 - 5.697) + \left[-0.110 - (-2.465)\right]x = -4.080 + 2.355x.
$$
You could also find this equation by having software use invertebrates as the baseline category:
```r
fit2 <- vglm(y ~ x, family=multinomial(refLevel="I"), data=Gators)
summary(fit2) # now using y=2 (I = Invertebrates) as baseline category
```
![[AlligatorExample-LogOddsEquation.png]]

#### Estimating Response Probabilities
For the baseline-category logit model (6.3), the response probabilities relate to the model parameters by
$$
\pi_j = \frac{e^{\alpha_j + \beta_{j1}x_1 + \beta_{j2}x_2 + \dots + \beta_{jp}x_p}}{\sum_{h=1}^{c} e^{\alpha_h + \beta_{h1}x_1 + \beta_{h2}x_2 + \dots + \beta_{hp}x_p}}, \quad j = 1, \dots, c.
$$

The denominator is the same for each $\pi_j$, and the numerators for various $j$ sum to the denominator, so $\sum_j \pi_j = 1$. The parameters equal zero for whichever category $j$ is the baseline in the logit expressions. For the estimates that contrast *fish* and *invertebrate* to *other* as the baseline category, the estimated probabilities of the outcomes (Fish, Invertebrate, Other) equal
$$
\begin{align}
\hat{\pi}_1 &= \frac{e^{1.618 - 0.110x}}{1 + e^{1.618 - 0.110x}
				+ e^{5.697 - 2.465x}} \\
\hat{\pi}_2 &= \frac{e^{5.697 - 2.465x}}{1 + e^{1.618 - 0.110x} 
				+ e^{5.697 - 2.465x}} \\
\hat{\pi}_3 &= \frac{1}{1 + e^{1.618 - 0.110x} + e^{5.697 - 2.465x}}
\end{align}
$$
***Using $\frac{e^\text{logit}}{1+e^\text{logit}}$ is non-sensical.  You must softmax all response probabilities.***

The 1 term in each denominator and in the numerator of $\hat{\pi}_3$ represents $e^{\alpha_3 + \beta_3x}$ for $\hat{\alpha}_3 = \hat{\beta}_3 = 0$ with category 3 as the baseline category.

For example, for an alligator of the maximum observed length of $x = 3.89$ meters, the estimated probabilities are $(\hat{\pi}_1, \hat{\pi}_2, \hat{\pi}_3) = (0.763, 0.005, 0.232)$. Very large alligators apparently prefer to eat fish. Software can display the estimated probabilities for each observation.
```r
fitted(fit)
```
![[AlligatorExample-ProbsOfEachObs.png]]

Figure 6.1 shows the three estimated response probabilities as a function of alligator length.
![[Fig6.1-EstimatedResponseProbs-AlligatorExample.png]]

#### Checking multinomial model goodness of fit
If the model has no continuous explanatory variables and the data is not sparse (most cell counts are at least about 5, in order to achieve a chi-square approximation), we can use the residual deviance to test the model goodness of fit.  As in the case of binary data, the deviance follows
$$G^2 = 2\Sigma\,\text{observed}[\log(\text{observed}/\text{fitted})]$$
A more informative check compares the working model to models that contain additional effects by comparing deviances.

**Example:** calculating degrees of freedom
![[Pasted image 20241017141112.png]]
![[belief in afterlife by gender and race.png]]
```r
Afterlife <- read.table("http://www.stat.ufl.edu/~aa/cat/data/Afterlife.dat", 
						header=TRUE)
Afterlife
```
![[AfterlifeDataset-Head.png]]
```r
library(VGAM)
fit <- vglm(cbind(yes, undecided, no) ~ gender + race, family=multinomial, 
            data=Afterlife)
summary(fit)
```
![[AfterlifeDataset-multinomialFit.png]]

Here, we have two binary variables and a $c=1$ categorical variable.  This gives us $2*2*(3-1) = 8$ degrees of freedom.  The model has 6 parameters, $\log(\frac{\pi_j}{\pi_3}) = \alpha_j + \beta_j^Gx_1 + \beta_j^Rx_2, j=1, 2$:
- We have $3-1 = 2$ reference levels of the Y (*no* is the baseline category for $Y$).
- Each of these models has two coefficients for the binary variables and an intercept.
- So, we have $2*3 = 6$ parameters in the model.

Our residual deviance of 0.85 then has $\text{df} = 8-6 = 2$.  We know this will be a good fit because of our informal test $0.85/2 < 1$, but we formally test this using `pchisq(0.85, 2)` and see it fits well.  An alternate model which drops `gender` from the model has a deviance of 8.05 ($\text{df} = 4$).  , and to see if this fit is better we test the difference of deviances and $\text{df}$ to get a p-value of `pchisq(8.05-0.85, 4-2)` $= 0.027$.  This shows evidence for a gender effect.

## Cumulative Logit Models for Ordinal Responses
#### Introduction
Cumulative Logit Models focus on *cumulative probabilities*, which represent the probability that a response falls in or below a specific category.  These are then transformed by the logit link, resulting in *cumulative logits* which are the log odds of the response being in or below a particular category vs. being above that category.

The *proportional odds assumption* is key here (\*Not all CL models use proportional odds), which assumes the effect of an explanatory variable is the same across all the different cumulative logits.  That is, we only have one set of odds ratios when we run an ordinal model, the only thing that changes is the intercept (shift along x-axis).  This is what simplifies the model down to a single parameter for the effect of an explanatory variable.

**Because the equation models the cumulative probability of $Y$ being leq category $j$, we interpret higher values of $\beta*x$ as lower odds of being higher than category $j$.**  That is, if our $Y$ is 1=least satisfied to 4=very satisfied, a subject is most likely to have highest job satisfaction when betas and values are high in the prediction equation.
#### Mathematical formalization
Logits can utilize the ordering in ordered response categories.  This results in models that have fewer parameters and potentially greater power and simpler interpretation than baseline-category logit models.  For the response variable $Y$, the cumulative probability for outcome category $j$ is
$$P(Y\leq j) = \pi_1 + \dots + \pi_j, \quad j=1, \dots, c$$
The cumulative probabilities reflect the ordering, with
$$P(Y \leq 1) \leq P(Y\leq 2) \leq \dots \leq P(Y\leq c) = 1$$
The logits of the cumulative probabilities, called *cumulative logits*, are
$$
\text{logit}[P(Y \leq j)] = 
	\log \left[ \frac{P(Y \leq j)}{1 - P(Y \leq j)} \right] = 
	\log \left( \frac{\pi_1 + \dots + \pi_j}
					 {\pi_{j+1} + \dots + \pi_c} \right)
$$
for $j=1, \dots, c-1$.  For $c=3$, for example, the two cumulative logits are
$$
\text{logit}[P(Y \leq 1)] = \log \left( \frac{\pi_1}{\pi_2 + \pi_3} \right)
\quad \text{and} \quad
\text{logit}[P(Y \leq 2)] = \log \left( \frac{\pi_1 + \pi_2}{\pi_3} \right).
$$
Cumulative logits and models for them do not use $P(Y \leq c)$, because it necessarily equals 1.

#### Cumulative Logit Models with Proportional Odds
A model for cumulative logit $j$ looks like a binary logistic regression model in which categories 1 to $j$ combine to form one category and categories $j+1$ to $c$ form the other.  For an explanatory variable $x$, the model
$$\text{logit}[P(Y \leq j)]\ = \alpha_j + \beta x, \quad j=1, \dots, c-1 
\hspace{4em}(6.4)$$
has parameter $\beta$ describing the effect of $x$ on the log odds of response in category $j$ or below.  The model assumes that the effect of $x$ is identical for all $c-1$ cumulative logits.  When this model fits well, it only requires a single parameter to describe the effect of $x$.  By contrast, the baseline-category logit model requires $c-1$ parameters, one for each logit.

Figure 6.2 below depicts this model for a four-category response and quantitative $x$.  Each cumulative probability has its own curve.
![[Figure6.2-CumulativeProbsinCumulativeLogitModel.png]]

![[Figure6.3-CategoryProbs-CumulativeLogit.png]]

#### Proportional Odds
We can test this assumption with a **test of parallel lines**.  If it doesn't hold, we should treat $y$ as nominal and use the multinomial logit model.

## Summarizing predictive power: Classification Tables
We can make a table of predictions by assigning predicted probabilities greater than some threshold $\pi_0$ as 1, and cross-classify this with the true labels of the data.
