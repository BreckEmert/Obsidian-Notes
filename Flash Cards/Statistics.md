#flashcards/Stats

Two goals of statistics research
?
1) Draw valid conclusions of an independent variable on a dependent variable.
2) Make valid generalizations to populations and/or settings of interest.

6 steps in research that are error-prone
?
1) Research question formulation
2) Study design
3) Implementation
4) Analysis
5) Inference
6) Interpretation

Two characteristics of sound research include \_ and \_ validity.
?
**Internal validity**: the accuracy of our conclusion that an independent variable is responsible for the variation in the dependent variable.
**External validity**: the ability to generalize our research findings across populations and settings.

Describe Sample, Observational, study designs.
?
**Sample** - estimate a parameter for a broader population
**Observational** - Draw a connection between variables based on observations.  Prone to errors.  Good for rare events, but is subject to bias/confounding.
**Experiment** - Controlling variables & observing changes.  Prospective, so does not rely on historical record, and can be evidential of cause-effect relationships.  But, not always feasible, resource intensive, and is subject to attrition (missing data).

Non-parametric $\neq$ no variables, instead, \_.:::It means that the method does not require or assume  a specific distributional form of the variables.

3 factors influencing power
?
1) The test itself & statistical significance criterion used.
	- Increase alpha, use parametric tests when assumptions are met, use one-tailed tests when appropriate.
2) Magnitude of the Effect
	- Raise treatment levels, reduce within-group variability through screening, matched pairs.
3) Size and variability of the Sample
	- How increased sample size translates to higher power = efficiency
	- More accurate measurements, blocking

Study biases
?
**Recall bias** - parents of children w/ birth defects may remember exposures better
**Interviewer bias** - interviewer who is aware of exposure status may probe expectationally

Full Pearson Correlation formula
?
$$\rho_{X,Y} = \frac{\mathbb{E}[(X - \mathbb{E}[X])(Y - \mathbb{E}[Y])]}{\sigma_X \sigma_Y}$$
or
$$r = \frac{\sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum_{i=1}^{n} (x_i - \bar{x})^2} \sqrt{\sum_{i=1}^{n} (y_i - \bar{y})^2}}
$$

Claims that a non-normal dependent variable for OLS is problematic are True/False.  Elaborate.
?
**False.** A non-normal dependent variable in itself is not problematic for OLS estimation.
**Unbiasedness and Consistency:**
- Per the theorems of regression, the OLS estimator under the standard linear model assumptions is unbiased and consistent.
- These properties do not require the dependent variable (or the predictors) to be normally distributed.
**Distributional assumptions**
- Only made on the error term.  Transformations only allow the model to learn non-linear effects.
- For unbiasedness and consistency, only the zero-mean and other moment conditions on the errors are required - not their full distribution.
**Transformations and non-linearity:**
- Box-Cox transformations can help the model capture non-linear **relationships**, but they are not meant to address non-normality **in and of itself**.
- Can sometimes stabilize variance, but they are not very helpful for heteroskedasticity.  Instead we use robust standard errors or weighted least squares.

PCA:
- Downsides
- R vs S
- Formula & reconstruction formula
?
$$X_\text{reduced} = X_\text{centered} V_k$$
where:
- $X_\text{reduced} \in \mathbb{R}^{m \times k}$ is the reduced dataset
- $X_\text{centered} \in \mathbb{R}^{m \times n}$ is the mean-centered original dataset
- $V_k \in \mathbb{R}^{n \times k}$ contains the top $k$ principal components
**Limitations of PCA:**
- Captures only linear relationships
- Less interpretable when loadings aren't high or sparse
- Sensitive to scale when using covariance matrix, but automatically using correlation is a brutal way to standardize.  Maybe some features ***should*** have more weight, and corr ($S$ or $\Sigma$) doesn't do preprocessing such as removing skewness.

A GLM extends linear regression by introducing
a) a link function that \_
b) a distribution that \_
?
**a)** determines a curve that characterizes the mean of the dependent variable as a function of the independent variables
**b)** specifies how the values of the dependent variable are dispersed around the mean given by the curve
This means that the conditional distribution is important for choosing GLM distribution but the unconditional distributions (histograms) are not.  $Y$'s variance can increase with $X$.

A major risk in interpretation when using log-transformed variables
?
Log-transformed variables have bias in their mean.  In right-skewed data, the log-space mean has compressed large values more than small ones, so it has a lower mean than the original. 
You can apply bias correction directly: $\hat{Y} = \exp(\hat{Y^*} + \frac{\sigma^2}{2})$
- where $\sigma^2$ is the variance from the log-space regression
If the residuals are not normal, use a non-parametric method: $\hat{Y} = \exp(\hat{Y^*}) \times \frac{1}{n} \sum_{i=1}^{n} \exp(\epsilon_i)$

Naive log-transformation does not work with \_ skewness.
?
Left skewness.  Instead, use roots or Box-Cox transformations.
![[left-skew-log.webp]]

Covariance and Pearson correlation formula
?
$$\text{Cov}(X,Y) = \frac{1}{n-1} \sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})$$
$$r = \frac{\text{Cov}(X,Y)}{\sigma_X \sigma_Y}$$

Formula and brief description:
SSE
SSR
SST
MSR
MSE
$R^2$
F-stat
?
SST = $\sum (Y_i - \bar{Y})^2$        predictions vs. actuals
SSR = $\sum (\hat{Y}_i - \bar{Y})^2$        explained variance
SSE = $\sum (Y_i - \hat{Y}_i)^2$        total variance
SST = $SSR + SSE$        (df = p-1)
MSE = $\frac{SSE}{\text{df}_{\text{residual}}} = \frac{SSE}{n - p}$    (df = n-p)
MSR = $\frac{SSR}{\text{df}_{\text{regression}}} = \frac{SSR}{p - 1}$
R^2 = $1 - \frac{SSE}{SST}$
F = $\frac{MSR}{MSE}$

Why ever use $S$ instead of $R$ in PCA?
?
**It's a brutal way of standardizing the data**
- Even linear transformations result in a different projection, eigenvectors & values
- Does not remove skewness or higher-order
- Boosts noise; all variances are now equal
- Loses scale information; scale is sometimes relevant.
- Doesn't remove outliers

Orthogonal vs. oblique PCs - when and why?
?
**Orthogonal Components**
- More efficient dimension reduction: each new component captures unique variance
- Preserves original correlation matrix
**Oblique PCs**
- More flexible: can reveal underlying factors that genuinely correlate
- Improved interpretability (hotly debated, it just depends)

PCA facts
?
- Eigenvector convention in mathematics, SS(PC loadings) = 1.  Some software follows this, some multiply eigenvectors by $\sqrt(\text{eigenvalues})$.  Then SS(PC loadings) = variance explained by that component.
- Squared multiple correlation (SMC) (multiple $R^2$) helps identify uncorrelated variables.  Question whether these should be included, as low-correlation indicates we won't find a low-dim reduction of the data.

