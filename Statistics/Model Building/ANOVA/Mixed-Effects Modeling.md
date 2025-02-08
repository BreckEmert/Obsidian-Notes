Copied directly from https://ademos.people.uic.edu/Chapter17.html
1 Background Information
========================

Mixed models are especially useful when working with a within-subjects design because it works around the ANOVA assumption that data points are independent of one another. In a within subjects design, one participant provides multiple data points and those data will correlate with one another because they come from the same participant. Therefore, using a mixed model allows you to systematically account for item-level variability (within subjects) and subject-level variability (within groups).

**When to Use?** – Studies that obtain multiple measurements over time (longitudinal, time-series) or multiple trials per participant (within subjects) lend themselves well to mixed model analyses.

The following example will illustrate the logic behind mixed effects models.

1.1 Example: National Pizza Study
---------------------------------

Let’s say that we are interested in examining the effect of pizza consumption on people’s moods. Each participant provided an average number of pizzas consumed, and measurements are collected at 15 timepoints

*   Hypothetical sample size, **n = 30**
*   **DV**: Mood rating (scale)
*   **IV1**: Pizza consumption
*   **IV2**: Time points (Weeks, 1-10)

Here is some hypothetical data (code used to generate data can be found [here](https://github.com/RInterested/SIMULATIONS_and_PROOFS/blob/master/Athletes%20mixed%20effects)):

    ##   time subject    pizza      mood
    ## 1    1       1 26.01455  76.58837
    ## 2    2       1 36.27042 104.24614
    ## 3    3       1 33.86071  99.88899
    ## 4    4       1 28.90242  85.24618
    ## 5    5       1 27.87595  82.93232
    ## 6    6       1 27.90510  83.87483

**NOTE** - This is a within-subjects study. All participants are providing multiple measurements.

1.2 Important Terminology
-------------------------

Below are some important terms to know for understanding the statistical concepts used in mixed models:

### 1.2.1 Crossed & Nested Designs

**Crossed designs** refer to the _within-subject_ variables (i.e. timepoint, condition, etc.). Crossed designs occur when multiple measurements are associated with multiple grouping variables. In a completely crossed design, all subjects provide responses for all conditions/time-points.

*   Pizza study: We have subjects providing responses at 10 time points. Thus, we have a crossed design.

**Nested designs** refer to the _between-subject_ variable. Generally this is a higher-level variable that subjects or items are grouped under.

*   Pizza study: Not nested.

### 1.2.2 Fixed v. Random Effects

**Fixed effects** are, essentially, your predictor variables. This is the effect you are interested in after accounting for random variability (hence, fixed).

*   Pizza study: The fixed effects are PIZZA consumption and TIME, because we’re interested in the effect of pizza consumption on MOOD, and if this effect varies over TIME.

**Random effects** are best defined as noise in your data. These are effects that arise from uncontrollable variability within the sample. _Subject_ level variability is often a random effect.

*   Pizza study: Controlling for random effects of subject, pizza consumption, and effect of time on subject, all of which vary across participants.

**NOTE** - Predictor variables can be both fixed (i.e. causing a main effect/interaction) and random (i.e. causing variance/variability in responses). When building your models, you can treat your predictor as a fixed & random factor.

### 1.2.3 Slopes v. Intercepts:

To better understand slopes and intercepts it maybe helpful to imagine plotting the relationship between the IVs and DV for each subject.

**Intercepts**: The baseline relationship between IV & DV. Fixed effects are plotted as intercepts to reflect the baseline level of your DV.

*   Random intercepts: Variability in baseline measurements
    
    *   Pizza Study: Different baseline levels of pizza consumption across subjects
*   Fixed intercepts: Baseline variance is not affected
    
    *   Pizza study:

**Slope**: The strength of the relationship between IV & DV (controlling for randomness), which represent random effects. You should expect to see differences in the slopes of your random factors.

*   Pizza study: The strength of the relationship between pizza consumption and mood will vary from person to person, resulting in random slopes per subject. Because subjects start at

**Note**: If 2 variables share a lot of variance, the random intercepts and slopes may be correlated with one another. This can be accounted for in random structures as well.

**Hypotheses For Study** Random effects: - “Subjects” will have their own intercepts. - Subjects’ slope will vary by pizza consumption intercepts, and by timepoint intercepts. - The slopes and intercepts of pizza consumption and time will be correlated (shared variance) Fixed effects: - Expecting there to be an overall main effect of pizza consumption over time. - Expecting interaction such that more pizza over time predicts mood.

2 Setting up data in R
======================

*   **Coding**: Recode your variable (mean-centered, effects) as best suited for your data.
*   **Long Format** : Refer to [TidyR chapter](http://ademos.people.uic.edu/Chapter9.html)
*   **Packages**: Make sure you have the following packages downloaded:

```r
library (lmerTest) # Mixed model package by Douglas Bates, comes w/ pvalues! 
library (texreg) #Helps us make tables of the mixed models
library (afex) # Easy ANOVA package to compare model fits
library (plyr) # Data manipulator package
library (ggplot2) # GGplot package for visualizing data
```

3 Modeling Procedure
====================

Modeling conventions differ by field, but this example will begin by fitting the null model first, then building up hierarchically.

3.1 Random effects structure
----------------------------

The _null model_ will be fit to the [maximal likelihood estimate](http://lme4.r-forge.r-project.org/lMMwR/lrgprt.pdf). The random effects structure reflects YOUR understanding of where to expect variance, and how nested data will interact with that variance. The general syntax is as follows:

```r
(1 + IV | unit level)  
(1 + IV.1*IV.2 | unit level)

#or

(0 + IV | unit level)
(0 + IV.1*IV.2 | unit level)
```

When there is a 1 before the line, you are accounting for random intercepts (varying baseline levels) in your variable. A O indicates the variable has a fixed intercept and not a random one. These are a few hypothetical random effects structures:

*   `(1| subject)` = Random intercepts and slopes for subjects (different baselines, different average effect per subject).
*   `(1 + pizza |subject)` = The effect of pizza will vary _between_ subjects. Random intercepts for pizza consumption, random slopes for subjects influenced by pizza consumption.
*   `(1 + pizza | subject) + (0 + time| subject)` = Subjects have random intercepts and slopes as influenced by pizza consumption. Time slopes can vary as function of the subject, but variance between pizza consumption and time as independent
*   `(1 + pizza + time | subject)` = Same as above, but variance between pizza consumption and time are SHARED (pizza consumption has relationship with time that varies by subject).
*   \``(1 + pizza * time | subject)` = Each subject can have their intercept, random slopes influenced by pizza and time, and their interaction between pizza and time. IMPORTANTLY, all random slopes and intercepts can be _correlated_.

### 3.1.1 Fitting Best Random Effects Structure

The `lmer` package can be used for modeling, and the general syntax is as follows: \`\`\` modelname <- lmer (dv ~ 1 + IV +(randomeffects), data = data.name, REML = FALSE)

\`\`\`

You can name each model whatever you want, but note that the name of the dataframe containing your data is specified in each model. Keep `REML = FALSE`.

First, however, we need to specify the random effects term that best fits the data. Try out different structures, and use the `anova` function to find the best fitting random effects structure. This function compares the fit of the model to see how fit has improved with additional items. You can also **visualize your data** to see what fits. ### Insert ggplot2 reference.

```r
nullmodel1 <- lmer( mood ~ 1 + (1|subject), data = pizzadata, REML=FALSE)
nullmodel2 <- lmer( mood ~ 1 + (1 + pizza |subject), data = pizzadata, REML=FALSE)
nullmodel3 <- lmer( mood ~ 1 + (1 + pizza * time |subject), data = pizzadata, REML=FALSE)
```

    ## Warning in optwrap(optimizer, devfun, getStart(start, rho$lower, rho$pp), :
    ## convergence code 1 from bobyqa: bobyqa -- maximum number of function
    ## evaluations exceeded

    ## Warning in checkConv(attr(opt, "derivs"), opt$par, ctrl = control
    ## $checkConv, : Model failed to converge with max|grad| = 4.09077 (tol =
    ## 0.002, component 1)

    ## Warning in checkConv(attr(opt, "derivs"), opt$par, ctrl = control$checkConv, : Model is nearly unidentifiable: very large eigenvalue
    ##  - Rescale variables?;Model is nearly unidentifiable: large eigenvalue ratio
    ##  - Rescale variables?

```r
anova (nullmodel1, nullmodel2, nullmodel3)
```

    ## Data: pizzadata
    ## Models:
    ## object: mood ~ 1 + (1 | subject)
    ## ..1: mood ~ 1 + (1 + pizza | subject)
    ## ..2: mood ~ 1 + (1 + pizza * time | subject)
    ##        Df    AIC    BIC   logLik deviance   Chisq Chi Df Pr(>Chisq)    
    ## object  3 2564.3 2575.4 -1279.15   2558.3                              
    ## ..1     5 1945.2 1963.7  -967.59   1935.2 623.116      2  < 2.2e-16 ***
    ## ..2    12 1923.2 1967.6  -949.58   1899.2  36.027      7  7.166e-06 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Refer to the p-values in the output to see whether there was an improvement in fit. Because there was an improvement in between model 1 and model 2, but NO improvement between model 2 and model 3, we can proceed using the best fit model, `nullmodel2`, as our random effects structure for the rest of the analyses.

3.2 Fixed effects
-----------------

Specific predictors can now be introduced into our model by specifying the DV followed by the predictor, random effects, and the dataframe.

**Model 1** - Pizza consumption predict mood (main effect):

```r
m1=lmer(mood ~ pizza + (1 + pizza + time |subject), data=pizzadata, REML = FALSE)
summary(m1)
```

    ## Linear mixed model fit by maximum likelihood t-tests use Satterthwaite
    ##   approximations to degrees of freedom [lmerMod]
    ## Formula: mood ~ pizza + (1 + pizza + time | subject)
    ##    Data: pizzadata
    ## 
    ##      AIC      BIC   logLik deviance df.resid 
    ##   1848.7   1882.0   -915.4   1830.7      291 
    ## 
    ## Scaled residuals: 
    ##     Min      1Q  Median      3Q     Max 
    ## -2.8112 -0.2712  0.0753  0.3161  2.3965 
    ## 
    ## Random effects:
    ##  Groups   Name        Variance  Std.Dev. Corr       
    ##  subject  (Intercept)  8.492055 2.91411             
    ##           pizza        0.227560 0.47703  -0.43      
    ##           time         0.003765 0.06136  -0.73 -0.30
    ##  Residual             16.177224 4.02209             
    ## Number of obs: 300, groups:  subject, 30
    ## 
    ## Fixed effects:
    ##             Estimate Std. Error      df t value Pr(>|t|)    
    ## (Intercept)  -3.0745     1.5974 34.1100  -1.925   0.0626 .  
    ## pizza         3.0179     0.1007 30.6000  29.975   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Correlation of Fixed Effects:
    ##       (Intr)
    ## pizza -0.608

This model appears to show pizza consumption as a positive predictor of mood, as indicated by a posi

Random effects:

*   SD reflects the amount of variation. Check correlation between intercept and slope (i.e. if intercept increases, slope increases).

Fixed effects

*   Check estimates for beta value – time has a significant effect, improvement in mood by about 1 point over time.
*   Check correlation of fixed effects – if too high, this may imply [multicollinearity](http://ademos.people.uic.edu/Chapter13.html)

**Model 2** – Pizza consumption and timepoints included as predictors of mood.

```r
m2= lmer(mood ~ pizza + time + (1 + pizza + time |subject), data=pizzadata, REML = FALSE)
summary(m2)
```

    ## Linear mixed model fit by maximum likelihood t-tests use Satterthwaite
    ##   approximations to degrees of freedom [lmerMod]
    ## Formula: mood ~ pizza + time + (1 + pizza + time | subject)
    ##    Data: pizzadata
    ## 
    ##      AIC      BIC   logLik deviance df.resid 
    ##   1838.7   1875.8   -909.4   1818.7      290 
    ## 
    ## Scaled residuals: 
    ##      Min       1Q   Median       3Q      Max 
    ## -2.58338 -0.34750  0.03691  0.27202  2.67350 
    ## 
    ## Random effects:
    ##  Groups   Name        Variance  Std.Dev. Corr       
    ##  subject  (Intercept) 2.371e+00 1.5398              
    ##           pizza       2.342e-01 0.4840   -0.85      
    ##           time        2.788e-04 0.0167    0.28 -0.74
    ##  Residual             1.562e+01 3.9517              
    ## Number of obs: 300, groups:  subject, 30
    ## 
    ## Fixed effects:
    ##              Estimate Std. Error        df t value Pr(>|t|)    
    ## (Intercept)  -0.69151    1.64377  28.43000  -0.421 0.677146    
    ## pizza         3.00716    0.10126  30.52000  29.698  < 2e-16 ***
    ## time         -0.28278    0.08025 261.23000  -3.524 0.000502 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Correlation of Fixed Effects:
    ##       (Intr) pizza 
    ## pizza -0.585       
    ## time  -0.380  0.037

```r
m2= lmer(mood ~ pizza + time + (1 + pizza + time |subject), data=pizzadata, REML = FALSE)
summary(m2)
```

    ## Linear mixed model fit by maximum likelihood t-tests use Satterthwaite
    ##   approximations to degrees of freedom [lmerMod]
    ## Formula: mood ~ pizza + time + (1 + pizza + time | subject)
    ##    Data: pizzadata
    ## 
    ##      AIC      BIC   logLik deviance df.resid 
    ##   1838.7   1875.8   -909.4   1818.7      290 
    ## 
    ## Scaled residuals: 
    ##      Min       1Q   Median       3Q      Max 
    ## -2.58338 -0.34750  0.03691  0.27202  2.67350 
    ## 
    ## Random effects:
    ##  Groups   Name        Variance  Std.Dev. Corr       
    ##  subject  (Intercept) 2.371e+00 1.5398              
    ##           pizza       2.342e-01 0.4840   -0.85      
    ##           time        2.788e-04 0.0167    0.28 -0.74
    ##  Residual             1.562e+01 3.9517              
    ## Number of obs: 300, groups:  subject, 30
    ## 
    ## Fixed effects:
    ##              Estimate Std. Error        df t value Pr(>|t|)    
    ## (Intercept)  -0.69151    1.64377  28.43000  -0.421 0.677146    
    ## pizza         3.00716    0.10126  30.52000  29.698  < 2e-16 ***
    ## time         -0.28278    0.08025 261.23000  -3.524 0.000502 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Correlation of Fixed Effects:
    ##       (Intr) pizza 
    ## pizza -0.585       
    ## time  -0.380  0.037

Results show significant effects of both pizza consumption and time on mood! Do they interact?

**Model 3** – Including an interaction term between pizza consumption and time (pizza consumption varies over time)

```r
m3 = lmer(mood ~ pizza*time + (1 + pizza + time |subject), data=pizzadata, REML = FALSE)
summary(m3)
```

    ## Linear mixed model fit by maximum likelihood t-tests use Satterthwaite
    ##   approximations to degrees of freedom [lmerMod]
    ## Formula: mood ~ pizza * time + (1 + pizza + time | subject)
    ##    Data: pizzadata
    ## 
    ##      AIC      BIC   logLik deviance df.resid 
    ##   1838.3   1879.0   -908.1   1816.3      289 
    ## 
    ## Scaled residuals: 
    ##      Min       1Q   Median       3Q      Max 
    ## -2.75864 -0.37286  0.05657  0.28274  2.58913 
    ## 
    ## Random effects:
    ##  Groups   Name        Variance  Std.Dev. Corr       
    ##  subject  (Intercept) 12.354060 3.51483             
    ##           pizza        0.239391 0.48928  -0.52      
    ##           time         0.002575 0.05074  -0.82 -0.06
    ##  Residual             15.272630 3.90802             
    ## Number of obs: 300, groups:  subject, 30
    ## 
    ## Fixed effects:
    ##              Estimate Std. Error        df t value Pr(>|t|)    
    ## (Intercept)   4.01103    3.36380 160.29000   1.192   0.2349    
    ## pizza         2.84985    0.14023  95.05000  20.323   <2e-16 ***
    ## time         -1.17445    0.54915 266.07000  -2.139   0.0334 *  
    ## pizza:time    0.03016    0.01833 265.56000   1.645   0.1011    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Correlation of Fixed Effects:
    ##            (Intr) pizza  time  
    ## pizza      -0.811              
    ## time       -0.877  0.685       
    ## pizza:time  0.856 -0.686 -0.989

Results show that while pizza consumption and time are still significant main predictors, their interaction term did not reach significance.

3.3 Comparing Model Fit
-----------------------

The ANOVA function allows you to compute Chi-squares between each model to see the improvement in model fit. The `effects` package should also include p-values in the output.

```r
anova (m1, m2, m3)
```

    ## Data: pizzadata
    ## Models:
    ## object: mood ~ pizza + (1 + pizza + time | subject)
    ## ..1: mood ~ pizza + time + (1 + pizza + time | subject)
    ## ..2: mood ~ pizza * time + (1 + pizza + time | subject)
    ##        Df    AIC    BIC  logLik deviance   Chisq Chi Df Pr(>Chisq)    
    ## object  9 1848.7 1882.0 -915.35   1830.7                              
    ## ..1    10 1838.7 1875.8 -909.37   1818.7 11.9663      1  0.0005417 ***
    ## ..2    11 1838.3 1879.0 -908.14   1816.3  2.4546      1  0.1171829    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

As you can see by the p-values, while there is an improvement in fit from model 1 to model 2, model 3 did not explain more variance. As such, model 2 appears to be the best fit.

We can now conclude that after controlling for random effects, more pizza consumption does lead to improvements in mood over time, but there is no interaction with time.

This concludes the tutorial on mixed effects models. Below are references for additional information # References [Checking assumptions](http://ademos.people.uic.edu/Chapter18.html) [More theory here](http://www.stat.cmu.edu/~hseltman/309/Book/chapter15.pdf), [here](http://jakewestfall.org/misc/BDB2008.pdf), and [here](http://www.bodowinter.com/tutorial/bw_LME_tutorial2.pdf). [Effects coding](http://www.martijnwieling.nl/R/sheets.pdf) [Simulating data](http://anythingbutrbitrary.blogspot.in/2012/10/hierarchical-linear-models-and-lmer.html)