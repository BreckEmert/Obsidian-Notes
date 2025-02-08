![[what is survival analysis.png]]

**Almost anything can be framed as the event of interest, so survival analysis has broad applications**


## Survival function
- One of the goals of survival analysis is to estimate the probability that a subject survives without experiencing the event past some time $t$.
- We can infer these probabilities from observing how long different subjects remain at risk before failing, i.e., observing their survival times.
- Let $T$ be a random variable representing a subject's true survival time.
- The survival function, $S(t)$, expresses the probability that a subject's true survival time $T$ will exceed time $t$, i.e., that the subject will survive beyond time $t$.

![[basic survival function.png]]
where:
- $T$ is the actual survival times
- $t$ is some particular time point

- The **median survival time** is the time at which 50% of the population is expected to still be surviving.  `survfit` provides this number by default in `print(model)`.


## Hazard function
Also known as the hazard rate or the instantaneous failure rate, gives the rate at which subjects are experiencing the event at time $t$, given that they have survived up to time $t$.  It describes how the survival function changes over time.

The survival function $S(t)$ can be derived from the hazard function $h(t)$.  This is because $S(t)$ is the probability of surviving up to time $t$, and $h(t)$ tells us how quickly subjects are failing (or experiencing the event) at each point in time.  Mathematically, this relationship is expressed as:
$$S(t) = \exp\left(-\int_0^t h(u) \, du\right)$$
The hazard function is inversely related to survival.  In other words, the higher the hazard, the lower the survival.

#### Cumulative Hazard Function
The cumulative hazard function, $H(t)$, expresses how much hazard a subject has accumulated over time up to time $t$. $$ H(t) = \int_0^t h(u) \, du $$
- The probability that a subject will fail over time increases as the hazard accumulates. 
- Because the hazard function $h(t)$ is never negative, the cumulative hazard $H(t)$ can never decrease with time.

![[hazard vs cumulative hazard graph.png]]
![[cumulative hazards and corresponding survival functions.png]]


## Censoring
Sometimes, we cannot observe a subject's true survival time $T$ during the course of a study, known as *censoring*.  Censoring occurs when the event of interest (e.g. death, relapse, etc.) has not been observed for some subjects by the end of the study period or when subjects are lost to follow-up for various reasons (e.g. they drop out of the study or the study ends before the event occurs).

In general, we say we observe subject's *follow-up time*, which for some will be the survival time $T$ and for others will be the censoring time.

There are three main types of censoring:
1. **Right Censoring**: This is the most common type, where we know that a subject has survived up to a certain time, but we don't know what happens after that.  For example, if a study ends after five years, and a subject is still alive at that point, we only know they survived up to the five-year mark, but not beyond it.  Or, if a subject is no longer "at risk" for the event after the study begins.
2. **Left Censoring**: This occurs when we know the event happened before a certain time, but we don't know exactly when.  For instance, if a subject had a relapse before entering the study, but we don't know the exact date, this is left censoring.  Disease infection is another common example, where positive tests for the infection may be delayed by days or even years.
3. **Interval Censoring**: This happens when we know the event happened with a certain time interval, but not exactly when.  For example, if a subject only visits the clinic every six months, and the even occurs sometime between visits, we only know that it occurred within that interval.  Or, if a subject tests negative for covid, and later positive, they must have been infected between those two points.
![[censoring example.png]]

#### Assumption of non-informative censoring
Most survival analysis methods assume non-informative censoring.
- A subject's censoring time should not be related to the unobserved survival time
- The distribution of censoring times and survival times should be unrelated

![[left vs right censoring.png]]


Failing to account for informative censoring may result in biased estimates of survival.  Below are plots of the Kaplan-Meier survival function estimates of the above data:
![[Pasted image 20240902221930.png]]

Examples of informative censoring, and the bias if not addressed:
- Oldest subjects drop out of study on 'time to death after surgery'.  They might have the shortest survival times, so survival estimates might be biased upward
- Travel-loving subjects drop out of study on 'time to first marriage'.  They may delay marriage to travel and have longer survival times, so survival estimates might be biased downward.


## Data for survival analysis
The simplest data structure for a typical survival analysis is:
- a single row per subject
- a status variable coding whether the subject experienced the event or not (censored)
- a single time variable measuring time to event (or censoring time)
- variables for covariates, assumed to be time-constant (not time-variant) in this structure

#### The `Surv()` function for survival outcomes from `library(survival)`
- Use `Surv()` to specify the survival outcome variables.
- Allows for many different time and event status configurations.
- For data with a single time variable indicating time to event or censoring, the `Surv` specification will be `Surv(time, event)`
	- `time` survival/censoring time variable
	- `event` status variable. To code for censored/event use:
		- 0/1
		- 1/2
		- False/TRUE
	- Censoring is assumed to be right-censored unless otherwise specified with the `type` argument.

`survival_object <- Surv(time = dataset$time, event = dataset$status)`

#### Start-stop format
Sometimes data is in a start-stop format, and contains 2 variables that mark the beginning and end of time intervals.  We need this format to model:
- time-varying covariates
- interval censoring
- recurrent events data
In this format, some or all subjects may have multiple rows of data.  This is the *start-stop* format, also known as *time-dependent* or *time-varying* survival data..

![[Pasted image 20240903115139.png]]
Note that `id` 4 contains two rows.  The `transplant` variable is used to model time-varying covariates.  0-35 indicates the time of `transplant = 0`, and once they got the transplant at `time = 35`, the event happened at `stop = 38`.


## Kaplan-Meier estimator of the survival function
This is a naive formula which is simply the product of proportions of people at risk who survive each time point $t_i$ up to the current time point $t$.  This is non-parametric, so it assumes no particular distribution for the survival times.  Instead, it relies on the data itself.
$$\hat{S}(t) = \prod_{t_i<t}(1 - \frac{events_i}{num.at.risk_i})$$
Survival estimates do not change if someone drops due to censoring, although the number at risk will drop.

#### Kaplan-Meier estimation with `survfit()`
- We can obtain the KM estimate of $S(t)$ using `survfit()`.
- The first argument is a model formula with a `Surv()` outcome specification on the left side of `~`.
- To estimate the survival function for the entire dataset, we specify `1` after `~`.
- `KM <- survfit(Surv(time, status) ~ 1, data=aml)`
- Printing the `survfit` object gives a summary:
	- **n**: total number at risk
	- **events**: total number of events that occurred
	- **median**: survival time at which `S(t)` = 0.5, or the time at which 50% of those at risk are expected to still be alive.
	- **0.95LCL, 0.95UCL**: the 95% confidence limits for median survival

![[survfit results 2.png]]

**The `tidy()` function from the `broom` package works with many of the output objects created by the `survival` package to create tables stored as tibbles.**

Using `tidy()` on the `survfit()` object produces a table of the KM estimate of the survival function `S(t)`.  In this table, `estimate` is calculated as $\hat{S}(5) = (1 - \frac{2}{23}) = .913$ and $\hat{S}(8) = (1 - \frac{2}{23})(1 - \frac{2}{21}) = .826$.

![[km surv function as tibble.png]]

Note that if the last person is censored in your dataset, the estimate may not go all the way to 0.

#### Stratified Kaplan-Meier estimates
To estimate separate KM survival functions for different strata, specify one or more strata variables after the `~` in `survfit()`.  Note that if the center of one strata's confidence interval overlaps with the center of the other strata, the groups are probably not different.  You're only safe when they're completely non-overlapping.
![[survfit results.png]]

The `tidy()` table adds on a `strata` column specifying the `x=strata_name`.


## Graphing stratified KM estimates of survival
`plot()` will only produce confidence intervals by default if one `S(t)` is plotted.
For multiple curves, we must request confidence intervals with `conf.int=TRUE`, and it's nice to specify two colors to make the graph more readable.
```
plot(KM.x, ylab="survival probability", xlab="months", 
     conf.int=TRUE, col=c("red", "blue"))
```
![[Pasted image 20240903130334.png]]

#### The `survminer` package
The `survminer` packages leverages `ggplot2` and adds many features.  Using `ggsurvplot()` from `survminer` on a `survfit` object produces a plot of the KM estimated survival functions.
`ggsurvplot(KM.x, conf.int=TRUE)`
![[Pasted image 20240903130613.png]]

Notice that `ggsurvplot()` automatically adds `+` symbols to denote censored observations.

`ggsurvplot()` makes adding a risk table very easy:
`ggsurvplot(KM.x, conf.int=TRUE, risk.table=TRUE)`
![[Pasted image 20240903130732.png]]

You can pass most arguments to various functions in `ggplot2` through `ggsurvplot()`.

```
ggsurvplot(KM.x, conf.int=TRUE, risk.table=TRUE, 
           palette="Accent",  # argument to scale_color_brewer()
           size=2,  # argument to geom_line()
           ggtheme=theme_minimal())  # changing ggtheme
```
![[Pasted image 20240903131059.png]]

You can use traditional `ggplot2` syntax by extracting the `ggplot` object as `ggsurvplot()$plot`.  To maintain the full object, including the `$plot` *and* `$table`, just modify the portions.  This is my preferred basic look for plotting survival functions:
```
km_plot <- ggsurvplot(km, conf.int=TRUE, risk.table=TRUE, 
                      palette="Accent", 
                      size=1,
                      ggtheme=theme_classic())

km_plot$plot <- km_plot$plot + 
  theme_classic() + 
  theme(panel.grid.major.y = element_line(color = 'grey80'))
```

To change the labels of the `Strata` legend, you can modify the source data like:
```
veteran$trt <- factor(veteran$trt, 
                      levels = c(1, 2), 
                      labels = c("Standard", "Test"))
```
Or, you can modify it with `legend.labs` in your `ggsurvplot`.
`legend.labs=c("male", "female")`


## Comparing survival functions with `survdiff()`
We often want to test the hypothesis:
$$
\begin{align}
& H_0: \text{survival curves across 2 or more groups are equivalent} \\  
& H_A: \text{survival curves across 2 or more groups are not equivalent}
\end{align}
$$
The log-rank statistic is a popular method to evaluate this hypothesis.
- Under the null, the log-rank statistic is $\chi^2$ distributed with $g - 1$ degrees of freedom, where $g$ is the number of groups.
- The function `survdiff()` performs the log-rank test by default.
![[survdiff results 2.png]]


## Weighted calculations
Sometimes, weighing earlier time points in survival analysis can reduce noise from unrelated events that might occur later on.  In a study comparing survival after different surgical procedures, if a patient dies within the first few weeks after surgery, the cause is more likely to be related to the surgery.  If a patient dies years later, the cause might be something entirely unrelated.  Weighing the earlier time points more heavily here allows the analysis to more accurately reflect the immediate risks associated with the surgery and filter out unrelated deaths occurring later.

`survdiff()` allows weights in calculation of the $\chi^2$ statistic with the `rho=` argument.  The weights are defined as $\hat{S}(t)^p$, where $0 \leq p \leq 1$.
- `rho=0` equal weights, $\hat{S}(t)^0 = 1$, which is the log-rank test and the default.
- `rho=1` the weights are equal to the survival estimate itself, $\hat{S}(t)^1 \hat{S}(t)$, equivalent to the Gehan-Wilcoxson test.
- `rho=` values between 0 and 1 are valid, with values closer to 1 putting more weight on earlier time points.
![[survdiff results.png]]


## The Cox proportional hazards model
- Instead of estimating the survival function $S(t)$ directly, the Cox proportional hazards model estimates changes to the hazard function, $h(t)$.  So, it's modeling *effects* on the hazard function.
- The Cox model can estimate the effects of multiple predictors on the hazard function.  Note that *predictor* = *covariate* in this context.
- By far the most popular method for survival analysis:
	- No distribution assumed for survival times
	- Naturally accommodates right-censoring and time-varying covariates
	- Can be extended in many ways:
		- time-varying coefficients (the effect of predictors is allowed to change over time)
		- random effect frailties for recurrent events or clustering
		- competing risks modeling
- However, it does assume that the hazards are proportional (visualized in [[#Proportional hazards]]).
- Also assumes $\log(\text{hazard})$ has a linear relationship with the predictors.

**Cox model is only interested in comparing hazards between subjects**

For simplicity, we begin with a Cox model with a single time-constant predictor $X_1$:
$$h(t|X_1 = x_1) = h_0(t)\exp(b_1x_1)$$
- $h(t|X_1 = x_1)$: the hazard at time $t$ for a subject with predictor $X_1$ equal to the value $x_1$
- $h_0(t)$: the baseline hazard at time $t$; the hazard for a subject with all predictors equal to zero (in this case, $x_1=0$).  **Note that all calculations using $h_0(t)$ cancel out, as the Cox model doesn't need to know what this hazard function is.  The hazard can be constant or changing.**
- $\exp(b_1x_1)$: the *hazard ratio* comparing the hazard for a subject with $X_1 = x_1$ to a subject with $X_1 = 0$
![[fit cox model without knowing shape.png]]


## Hazard ratio
Imagine that $X_1$ is a treatment variable, with values $X_1 = 1$ for treatment and $X_1 = 0$ for control.
The hazard at time $t$ for treatment (sub in $x_1 = 1$):
$$
\begin{align}
h(t|X_1 = 1) & = h_0(t)\exp(b_1 * 1) \\
			 & = h_t(t)\exp(b_1)
\end{align}
$$
and for control (sub in $x_1 = 0$):
$$
\begin{align}
h(t|X_1 = 0) & = h_0(t)\exp(b_1 * 0) \\
             & = h_0(t)\exp(0) \\
			 & = h_0(t)
\end{align}
$$
We can compare the hazards for treatment and control at time $t$ as a *hazard ratio* (HR):
$$
\begin{align}
\text{HR} & = \frac{h(t|X_1 = 1)}{h(t|X_1 = 0)} \\
          & = \frac{h_o(t)\exp(b_1)}{h_o(t)} \\
          & = \exp(b_1)
\end{align}
$$
Thus, $\exp(b_1)$ is the hazard ratio comparing hazard for treatment to controls.
- $\text{HR} = .25$ means that treatment has $\frac{1}{4}$ (25%) the hazard of control, or a 75% decrease.  With a lower hazard rate, treatment will have fewer expected events and thus better survival.
- $\text{HR} = 2$ here means that treatment has twice the hazard of control, or a 100% increase, and thus worse survival.

In general, $\exp(b_1)$ expresses the hazard ratio for a 1-nit increase in the associated covariate.
$b_1$ itself is the log-hazard ratio.

#### Remember, it's the cumulative hazard that determines survival, not instantaneous hazard.  Doubling hazard does not necessary halve survival.  Hazard is just events per unit time.


## Proportional hazards
The standard Cox model assumes proportional hazards, which means that the effects of covariates are constant over time.  For example, in our simple Cox model for a treatment effect, proportional hazards means that the effect of treatment does not change over time.  Notice that the expression for the hazard ratio for the treatment effect does not contain time, so it will be the same value no matter the time:
$$\text{HR} = \exp(b_1)$$

**Note**: With proportional hazards, a subject's *hazard function* (which we don't need to know for the Cox model) can change over time.  But the hazard ratio comparing that subject's hazard to another subject's hazard cannot change over time, provided their covariate values do not change.

Visually, this is represented by "parallel" survival curves that should not cross:
![[proportional hazard functions 2.png]]

The parallelism is easier to evaluate if we plot $-\log-\log(S(t)))$.  If the hazards are proportional, the vertical distance between the curves is constant across time.
![[proportional hazard functions.png]]
![[nonproportional hazard functions.png]]


#### Assessing the proportional hazards assumption
Several methods have been developed to assess the proportional hazards assumption of the Cox model.  Here we discuss two tools developed by Grambsch and Therneau (1994).

**A chi-square test based on Schoenfeld residuals** is available with `cox.zph()` to test the hypothesis:
$$
\begin{align}
& H_0: \text{covariate effect is constant (proportional) over time} \\
& H_A: \text{covariate effect changes over time}
\end{align}
$$
The null hypothesis of proportional hazards is tested for each covariate individually *and* jointly.

`cox.zph(lung.cox)`
![[chisq age sex wt loss.png]]
No strong evidence of violation of proportional hazards for any covariate, though some suggestion that sex may violate.  Remember that `lung.cox` came from:
`lung.cox <- coxph(Surv(time, status) ~ age + sex + wt.loss, data=lung)`

Another tool used to assess proportional hazards is a plot of a smoothed curve over the Schoenfeld residuals.
- To create this plot, `plot()` the object returned by `cox.zph()`.
- These plots depict an estimate of the coefficient (labelled "Beta(t)" in the plot)(y-axis) across time (x-axis).
- Proportional hazards is indicated by a flat line.
- `plot(cox.zph(lung.cox))`

This plot is of a zph $p=0.48$, so it is a good depiction of flat.  Although the line is a bit curvy, the trend is indiscernible:
![[Pasted image 20240903230625.png]]

This plot approaches a zph $p=0.05$, but is only $p=0.11$.  Note that the estimate from the Cox model was -0.5, an average of the linear trend.  We expect a slight visual trend:
![[Pasted image 20240903230534.png]]

#### Dealing with proportional hazards violations
Many strategies have been proposed to account for a violation of PH.  Here are two:
- stratify by the non-PH variable
- add an interaction of the non-PH variable with time to the model

If the change in the coefficient is not large enough to be clinically meaningful, it can perhaps be ignored as well.

Imagine we are interested in the effect of weight loss (`wt.loss`) on survival, but are also concerned that possible PH violation by sex in our Cox models may bias estimates of the wt.loss effect.

We can perform a sensitivity analysis to show how our inferences regarding the `wt.loss` effect change depending on whether we address the PH violation by sex or not.

In the standard Cox model assuming PH for sex, there was not much evidence that `wt.loss` was strongly predictive of survival, and we cannot even be confident about the direction of the effect.


## Baseline hazard function $h_0(t)$
As mentioned before, the Cox model does not need to know $h_0(t)$ nor its shape.  All of the calculations cancel out which contain $h_0(t)$.

We thus then do not need to estimate parameters to characterize a hazard function or survival distribution, but only the regression parameters that quantify the covariate effects.  The Cox model is thus called *semiparametric*.  There is no constant/intercept in Cox models.

Because $h_0(t)$ is left unspecified, the Cox model cannot directly estimate either the hazard function or the survival function, but is used to estimate the covariate effects on the hazard functions.


## Cox model with multiple predictors
The Cox is easily extended to accommodate multiple predictors, each of whose effects is assumed to be proportional over time.
$$h(t|X_1, X_2, \dots, X_p) = h_0(t)\exp(b_1X_1 + b_2X_2 + \dots + b_pX_p)$$
Each coefficient $b_i$ can be exponentiated to calculate a hazard ratio.


## Example implementation of the Cox model
Using the lung dataset:
- time: survival time in days
- status: 1=censored and 2=dead
	- `Surv()` accepts status with 1=censored and 2=event
- age: age in years (assessed at beginning)
	- Typically age is measured at the beginning in survival analysis.  It cannot be used as a time-varying covariate because it perfectly tracks with time and will have too much dependency.
- wt.loss: weight loss (pound) in last 6 months

```
lung <- survival::lung
head(lung)

lung.cox <- coxph(Surv(time, status) ~ age + sex + wt.loss, data=lung)
summary(lung.cox)
```

output:
![[coxph results.png]]

Remember that the `coef` column are the *log* hazard ratios.  **Always look at the number of events in the summary output, to make sure it's the number you expect.**

Generally, we interpret positive coefficients as increasing the log-hazard and lowering survival, and negative coefficients as decreasing the log-hazard and increasing survival.

Looking at the `exp(coef)` column:
- $\hat{HR}_{age} = 1.0203$, for each additional year of age at baseline, the hazard increases by 2.03%, or by a factor of 1.0203
- $\hat{HR}_{sex} = .5939$, females have 60% the hazard of males, or a 40% decrease
	- Thus, females have a higher survival.
- $\hat{HR}_{wt.loss} = 1.008$, for each additional pound of weight loss, the hazard increases by 0.08%

The p-value `Pr(>|z|)` test whether the coefficient is equal to 0.  If the confidence intervals of `exp(coef)` contain 1, this means that p > 0.05.

The last chunk of information in the output:
- **The concordance statistic**:
	- A measure of model goodness (predictive accuracy), the closer to 1, the better.
	- Evaluates how well the model's predicted hazard ranks correlate with the actual survival times, and is similar to the concept of AUC used in binary classification.
	- C-index is used to assess the model's ability to correctly rank the survival times based on the covariates.
		- A C-index of 0.5 indicates that the model's predictions are no better than random chance
		- A C-index of 1.0 indicates perfect prediction
- **Likelihood ratio test**:
	- **This is the standard test of model significance, explained in the following paragraph.**
	- Compares the likelihood of the data under the full model vs. the null model.
	- A significant p-value indicates that at least one of the covariates significantly contributes to the model, meaning it improves the fit over the null model.
- **Wald test**:
	- Assesses the significance of individual coefficients in the model.
	- It tests the null hypothesis that each coefficient is zero (i.e., that the covariate has no effect on the hazard).
	- Derived from the ratio of the coefficient estimate to its standard error
- **Score (log-rank test)**:
	- Evaluates the overall significance of the model
	- Compares the observed and expected survival times under the null hypothesis

Like in other areas of statistics, the LR test is the gold standard.  It's typically more accurate for all sample sizes.  One way this is the case is that even in full separation, the LR test is fully accurate whereas the standard errors in Wald tests blow up.  The LR test also provides m ore accurate confidence intervals using confidence profiles.  **If you don't want to make approximations, Bayesian inference is exact.**

#### Tidy `coxph()` results
We can `broom::tidy()` the `coxph()` results and store them in a tibble data.frame:
`lung.cox.tab <- tidy(lung.cox, exponentiate=TRUE, conf.int=TRUE)

Storing the Cox model results as a data.frame makes it easy to use `ggplot2` to create plots of the hazard ratios and confidence intervals:
```
ggplot(lung.cox.tab, aes(x=estimate, y=term, xmin=conf.low, xmax=conf.high)) + 
  geom_pointrange() +  # plots center point (x) and range (xmin, xmax)
  geom_vline(xintercept=1, color="red") +  # vertical line at HR=1
  labs(x="hazard ratio", title="Hazard ratios and 95% CIs") + 
  theme_classic()
```

(This plot is confusing because the CIs are tight and covering $\text{HR}=1$, but the red line shows that these two variables are not significant while `sex` is.)
![[hazard ratios and 95 cis.png]]


## Predicting survival with Cox estimates
We often want to estimate and compare survival functions for subjects with different sets of covariates.

Because the Cox model does not estimate survival directly, we first use non-parametric methods (similar to KM-estimator) to estimate the *baseline survival function* $S_0(t)$, the survival function for a subject with zero on all covariates.
$$S_0(t) = S(t|X_1 = 0, X_2 = 0, \dots, X_p = 0)$$
After we estimate the baseline survival function, $\hat{S}_0(t)$, we can then estimate the survival function for a subject with non-zero covariate values using the regression coefficients estimated from the Cox model and this relation:
$$\hat{S}(t|X_1 = x_1, X_2 = x_2, \dots, X_p = x_p) = \hat{S}_0(t)^{\exp(b_1x_1+b_2x_2+\dots+b_px_p)}$$
Because $\hat{S}_0(t)$ is estimated non-parametrically, survival functions estimated after `coxph()` will again be step functions that change values only at event times observed in the data.

#### The `survfit()` function, when supplied a `coxph`model object, performs all of the calculations to predict survival.

If no covariate values are supplied to `survfit()`, then a survival function will be estimated for a subject with mean values on all model covariates.

We can then use `tidy()` to produce a table of the survival function estimated by `survfit()`:
```
surv.at.means <- survfit(lung.cox)  # Predicting for someone with mean of all covariates
tidy(surv.at.means)
```
![[Pasted image 20240903214054.png]]

However, it's recommended to **not** use the default mean values, as this may make no sense.  Instead, specify values at which to predict.  In this case, we should predict survival separately for each of the sexes:
```
prediction_cases <- data.frame(
  age = mean(lung$age), 
  sex = 1:2, 
  wt.loss = mean(lung$wt.loss, na.rm=T)
)
prediction_cases
```
![[Pasted image 20240903223343.png]]

Then, we supply the new data to `survfit`, along with the model object created by `coxph()`.  We `tidy()` the `survfit` object to produce a table of predicted survival functions.  Note the column suffixes `.1` and `.2` that differentiate the survival, standard error, and confidence interval estimates between the 2 sexes.
```
# Get survival function estimates for each sex
surv.by.sex <- survfit(lung.cox, newdata=prediction_cases)
tidy(surv.by.sex)
```


## Plotting survival curves
We can also use `plot()` on the `survfit()` object
`plot(surv.at.means, xlab="days", ylab="survival probability")`
![[Pasted image 20240903214149.png]]

Note that using `ggsurvplot()` requires the specified cases dataframe `prediction_cases` again.  `censor=F` is specified because, in predicting, it doesn't make sense to have censors.
```
lung.cox.plot <- ggsurvplot(
  surv.by.sex, data=prediction_cases, censor=F, 
  legend.labs=c("male", "female"), 
  palette="Accent", 
  size=1, 
  ggtheme=theme_classic())

lung.cox.plot$plot <- lung.cox.plot$plot + 
  theme_classic() + 
  theme(panel.grid.major.y = element_line(color='grey80'))

lung.cox.plot
```
![[Pasted image 20240903224335.png]]

The hazard ratio comparing males to females was $\hat{HR}_{sex} = .594$, meaning that females have about 60% of the hazard rate that males do.  Females thus have better overall survival than males, depicted in the graph above.

#### For continuous variables, you just split the variable into as many curves as you want, and can plot those.  For example, `age` = 20, 40, and 60.


## Survival Distributions
#### Exponential Distribution
- $S(t) = \exp(-\lambda t), \quad f(t) = \lambda \exp(-\lambda t)$
- The hazard function is constant over time, meaning the risk of the event does not change.
- **Upside**: Simple to use and interpret.  
- **Downside**: Assumes constant hazard, which may not be realistic for recidivism analysis.
#### Weibull Distribution
- $S(t) = \exp(-(\lambda t)^p), \quad \lambda(t) = \lambda p t^{p-1}$
- The hazard function can either increase or decrease over time based on $p$.
- **Upside**: Flexible; can model both increasing and decreasing hazard rates.
- **Downside**: Assumes a specific form of hazard rate (increasing, decreasing, or constant).
#### Gamma Distribution
-  $f(t) = \frac{\lambda (\lambda t)^{k-1} e^{-\lambda t}}{\Gamma(k)}, \quad S(t) = 1 - I_k(\lambda t)$
- The hazard function can increase, decrease, or remain constant depending on the shape of $k$.
- **Upside**: Very flexible for modeling different hazard rate patterns.
- **Downside**: Complex to compute and interpret, lacks a simple closed-form survival function.
#### Log-Normal Distribution
- $Y = \log T = \alpha + \sigma W, \quad W \sim N(0, 1)$
- The hazard function rises to a peak and then decreases over time.
- **Upside**: Captures non-monotonic hazard rates, good for peaking hazards.
- **Downside**: Harder to interpret and fit compared to simpler models.
#### Log-Logistic Distribution
- $S(t) = \frac{1}{1 + (\lambda t)^p}, \quad \lambda(t) = \frac{\lambda p (\lambda t)^{p-1}}{1 + (\lambda t)^p}$
- The hazard rate can either decrease or increase and then decrease, similar to the log-normal distribution.
- **Upside**: Provides a flexible hazard function, useful for both increasing and decreasing hazards.
- **Downside**: Can be difficult to distinguish from the log-normal distribution.
