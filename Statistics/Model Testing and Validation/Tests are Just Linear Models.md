Notes taken directly from https://lindeloev.github.io/tests-as-linear/.
## The Simplicity Underlying Common Tests
Most common statistical models (t-test, correlation, ANOVA, etc.) are special cases of **linear models** or close approximations. This underlying simplicity means there's less to learn than it seems in traditional stats courses, which often introduce each test as a distinct tool. In reality, many tests can be reduced to linear models of the form: $y = a \cdot x + b$.

In teaching, we should emphasize this core idea, focusing on **understanding** linear models rather than learning assumptions for each test separately. Once students grasp the fundamentals of linear models, the specific tests can be introduced as special cases of this broader framework. Even many "non-parametric" tests can be understood in this way, as they are often just ranked versions of parametric tests.


## Correlation
#### Pearson correlation
The Pearson correlation can be represented as a simple linear model, especially because if the standard deviations of `x` and `y` are the same the slope equals `r`:
$$ y = \beta_0 + \beta_1 x \quad \mathcal{H}_0: \beta_1 = 0 $$

We compare the Pearson correlation to a basic `lm`:
```r
a = cor.test(y, x, method = "pearson") # Built-in
b = lm(y ~ 1 + x) # Equivalent linear model: y = Beta0*1 + Beta1*x
c = lm(scale(y) ~ 1 + scale(x))  # On scaled vars to recover r
```
![[Pearson-vs-lm.png]]

#### Spearman correlation
The **Spearman rank correlation** is a **Pearson correlation** applied to the ranks of $x$ and $y$:
$$ \text{rank}(y) = \beta_0 + \beta_1 \cdot \text{rank}(x) $$

We compare the Spearman correlation to a ranked `lm`:
```r
# Spearman correlation
a = cor.test(y, x, method = "spearman") # Built-in
b = lm(rank(y) ~ 1 + rank(x)) # Equivalent linear model
```
![[Spearman-vs-lm.png]]


Both Pearson and Spearman correlations are just linear models with either actual or ranked data.  This shows the original slope modeled on a basic normal, followed by what happens to the fit when the data is rank-transformed (original values replaced with their rank):
![[PearsonvSpearmanRankExample.png]]


## One mean
#### One-Sample t-Test
The **one-sample t-test** asks if a single number predicts $y$:
$$ y = \beta_0 \qquad \mathcal{H}_0: \beta_0 = 0 $$

We see that this tests whether the mean of $y$ is significantly different from zero. It's a simple intercept-only linear model:
```r
# Built-in t-test
a = t.test(y)

# Equivalent linear model: intercept-only
b = lm(y ~ 1)
```
![[Ttest-vs-lm.png]]

#### Wilcoxon signed-rank test
The Wilcoxon signed-rank test is a ranked version of the same model:
$$ \text{signed rank}(y) = \beta_0 $$

```r
# Built-in
a = wilcox.test(y)

# Equivalent linear model
b = lm(signed_rank(y) ~ 1)  # See? Same model as above, just on signed ranks

# Bonus: of course also works for one-sample t-test
c = t.test(signed_rank(y))
```
![[Wilcox-vs-lm.png]]

Both tests are fundamentally linear models, with or without rank transformation.
![[Ttest-Wilcoxon.png]]

#### Paired samples t-test
The t-test asks if a single number (intercept) predicts the pairwise differences:
$$y_2 - y_1 = \beta_0 \quad \mathcal{H}_0:\beta_0 = 0$$

This means that there is just one $y = y_2 - y_1$ to predict and it becomes a one-sample t-test on the pairwise differences.  We highlight the differences in pairs and why a t-test works here:
![[Pairs-vs-Ttest.png]]

And compare the t-test to a linear model here:
```r
a = t.test(y, y2, paired = TRUE) # Built-in paired t-test
b = lm(y - y2 ~ 1) # Equivalent linear model
```
![[PairedT-vs-lm.png]]

#### Wilcoxon matched pairs
Again, we do the signed-ranks trick.  This is an approximation, but a close one:
```r
# Built-in Wilcoxon matched pairs
a = wilcox.test(y, y2, paired = TRUE)

# Equivalent linear model:
b = lm(signed_rank(y - y2) ~ 1)

# Bonus: identical to one-sample t-test ong signed ranks
c = t.test(signed_rank(y - y2))
```
![[Wilcoxon-vs-lm.png]]


## Two means
#### Independent t-test
The (Student's) independent t-test asks if two means predict $y$:
$$ y_i = \beta_0 + \beta_1 x_i \quad \mathcal{H}_0: \beta_1 = 0 $$
- where $x_i$ is an indicator signifying which of the two groups $i$ was sampled from

Again, the independent t-test can be understood as a linear model:
```r
# Built-in independent t-test on wide data
a = t.test(y, y2, var.equal = TRUE)

# Be explicit about the underlying linear model by hand-dummy-coding:
group_y2 = ifelse(group == 'y2', 1, 0)  # 1 if group == y2, 0 otherwise
b = lm(value ~ 1 + group_y2)  # Using our hand-made dummy regressor

# Note: We could also do the dummy-coding in the model
# specification itself. Same result.
c = lm(value ~ 1 + I(group == 'y2'))
```
![[TwoMeansTtest-vs-lm.png]]

#### Mann-Whitney U
The **Mann-Whitney U test** (or Wilcoxon rank-sum test) is the same model applied to ranks (to a very close approximation).  It evaluates whether one distribution tends to have larger values than the other, instead of testing means like the independent t-test:
$$ \text{rank}(y_i) = \beta_0 + \beta_1x_i \quad \mathcal{H}_0: \beta_1 = 0 $$

And can be interpreted as a linear model on ranked data:
```r
# Wilcoxon / Mann-Whitney U
a = wilcox.test(y, y2)

# As linear model with our dummy-coded group_y2:
b = lm(rank(value) ~ 1 + group_y2)  # compare to linear model above
```
![[MannWhitneyU-vs-lm.png]]

#### Welch's t-test
This is identical to the (Student's) independent t-test above except that Student's assumes identical variances and Welch's t-test does not.  So the linear model is the same, but we model one variance per group.

```r
# Built-in
a = t.test(y, y2, var.equal = FALSE)

# As linear model with per-group variances
b = nlme::gls(value ~ 1 + group_y2, 
			  weights = nlme::varIdent(form = ~1|group), 
			  method = "ML")
```
![[WelchT-vs-lm.png]]


## Three or more means
#### One-Way ANOVA
The ANOVA model asks if one mean for each group predicts $y$:
$$ y = \beta_0 + \beta_1x_1 + \beta_2x_2 + \dots \quad \mathcal{H}_0: \beta_1 = 0 $$
- Where $x_i$ are indicators where at most one $x_i = 1$ while all others are 0.

Notice this continuation - when there are only two groups, this model is $y = \beta_0 + \beta_1*x$, the independent t-test.  If there is only one group, it is $y = \beta_0$, the one-sample t-test.  The one-way ANOVA has a log-linear counterpart, the goodness-of-fit test, discussed later.

```r
# Compare built-in and linear model
a = car::Anova(aov(value ~ group, D))  # Dedicated ANOVA function
b = lm(value ~ 1 + group_b + group_c, data = D)  # As in-your-face linear model
```
![[ANOVA-lm.png]]
(The `lm` shows the F-statistic for individual regression coefficients while the ANOVA F is testing if the group variable explains a significant amount of variance, hence why they're different.)

#### Kruskal-Wallis test
The Kruskal-Wallis test is simply a one-way ANOVA on a rank-transformed $y$:
$$ \text{rank}(y) = \beta_0 + \beta_1x_1 + \beta_2x_2 + \dots $$

This is an approximation, good enough for 12 or more data points.  If you did this for just one or two groups, it's the Wilcoxon signed-rank test and Mann-Whitney U test respectively.

```r
a = kruskal.test(value ~ group, D)  # Built-in
b = lm(rank(value) ~ 1 + group_b + group_c, D)  # As linear model
c = car::Anova(aov(rank(value) ~ group, D))  # The same model, using a dedicated ANOVA function which just wraps lm.
```
![[Kruskal-vs-lm.png]]

#### Two-Way ANOVA
The **two-way ANOVA** model extends the one-way model by adding interaction terms:
$$ y = \beta_0 + \beta_1X_1 + \beta_2X_2 + \beta_3X_1X_2 \quad \mathcal{H}_0: \beta_3 = 0$$
This tests for the main effects of two factors as well as their interaction.
```r
aov(y ~ factor1 * factor2)
```

This is another linear model with categorical predictors, but now includes interactions between factors.

#### ANCOVA
**ANCOVA** is just an ANOVA model with a continuous covariate:  $$ y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \beta_3 z $$
Where $z$ is the continuous covariate. In R:
```r
aov(y ~ factor + covariate)
```

This is a straightforward extension of ANOVA that adds a continuous predictor to the model.


## Proportions
Recall that when you take a logarithm, you can easily make statements about proportions: for every increase in $x$, $y$ increases a certain percentage.  This is a simple way to make count data and contingency tables intelligible.
#### Goodness of Fit
The goodness of fit model tests if a single intercept predicts $\log(y)$:
$$ \log(y_i) = \log(N) + \log(\alpha_i) $$
- Where $y$ is the count variable and $\alpha_i$ are the proportions.

Goodness of fit is just a log-linear equivalent to a one-way ANOVA:
```
# Built-in test
a = chisq.test(D$counts)

# As log-linear model, comparing to an intercept-only model
full = glm(counts ~ 1 + mood_happy + mood_sad, data = D, family = poisson())
null = glm(counts ~ 1, data = D, family = poisson())
b = anova(null, full, test = 'Rao')

# Note: glm can also do the dummy coding for you:
c = glm(counts ~ mood, data = D, family = poisson())
```
![[GoodnessOfFit-vs-GLM.png]]

The log-linear model tests whether the observed counts deviate significantly from expected counts.

#### Contingency Tables
For a two-way contingency table, the model of the count variable $y$ uses the marginal proportions of the contingency table.  The regression coefficients (which are proportions) independently contribute to $y$.
$$ \log(y_{i}) = \log(N) + \log(\alpha_i) + \log(\beta_j) + \log(\alpha_i \beta_j) $$

This is just the two-way ANOVA model with some logarithms added:
```r
# Built-in chi-square. It requires matrix format.
a = chisq.test(D_table)

# Using glm to do a log-linear model, 
# we get identical results when testing the interaction term:
full = glm(Freq ~ 1 + mood_happy + mood_meh + sex_male + mood_happy*sex_male + mood_meh*sex_male, data = D, family = poisson())
null = glm(Freq ~ 1 + mood_happy + mood_meh + sex_male, data = D, family = poisson())
b = anova(null, full, test = 'Rao')  # Could also use test='LRT' or test='Chisq'

# Note: let glm do the dummy coding for you
full = glm(Freq ~ mood * sex, family = poisson(), data = D)
c = anova(full, test = 'Rao')

# Note: even simpler syntax using MASS:loglm ("log-linear model")
d = MASS::loglm(Freq ~ mood + sex, D)
```
![[Contingency-vs-glm.png]]

#### Chi-square test
Both chi-square tests and log-linear models test the independence between categorical variables.

```r
# Built-in chi-square. It requires matrix format.
a = chisq.test(D_table)

# Using glm to do a log-linear model, we get identical results when testing the interaction term:
full = glm(Freq ~ 1 + mood_happy + mood_meh + sex_male + mood_happy*sex_male + mood_meh*sex_male, data = D, family = poisson())
null = glm(Freq ~ 1 + mood_happy + mood_meh + sex_male, data = D, family = poisson())
b = anova(null, full, test = 'Rao')  # Could also use test='LRT' or test='Chisq'

# Note: let glm do the dummy coding for you
full = glm(Freq ~ mood * sex, family = poisson(), data = D)
c = anova(full, test = 'Rao')

# Note: even simpler syntax using MASS:loglm ("log-linear model")
d = MASS::loglm(Freq ~ mood + sex, D)
```
![[ChiSq-vs-glm.png]]