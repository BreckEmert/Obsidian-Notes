## $e(x) = \mathbb{P} \left[ W_i = 1 \mid X_i = x \right]$
i.e., the propensity score measures the **probability of being treated** conditionally on $X_i$

Propensity scores reduce selection bias in observational studies where random assignment isn't possible.  They estimate the probability (*propensity*) that a subject would receive a treatment given their observed characteristics, allowing us to mimic some properties of a randomized experiment.
### How propensity scores work
1) **Score calculation**: A model (often logistic regression) estimates the likelihood that a subject would receive the treatment based on covariates (pre-treatment characteristics).
2) **Using them in a model**: Subjects can be matched, weighted, or stratified based on these scores to balance the **observed** covariates between treated and untreated groups.
### Advantage:
- **Reduced bias**: Propensity scores balance covariates between groups, reducing confounding in the estimation of treatment effects.
### Disadvantage:
- **Unobserved confounding**: We are **not** replicating a randomized experiment with this method.  We only control for **observed** confounders.
- **Loss of data**: Matching methods can result in data loss if many units don't have similar counterparts, especially when the groups are quite different.

In a **randomized trial**, the propensity score is constant.
$e(x) = e_0 \in (0, 1)$
- At least qualitatively, the variability of the propensity score gives a measure of how far we are from a randomized trial.


## Methods of application

### 1) Matching
Here, we match individuals in the treatment group with individuals in the control group who have similar propensity scores.  This allows for a direct comparison between treated and untreated individuals who are similar in their observed characteristics.

Matching is useful for when we want to compare individuals most balanced in their covariates compared to weighting and stratifying methods.  The resulting groups are more directly comparable.  Matching also reduces the influence of poorly matched results that could skew model results.  However, this method is subject to data loss, and we will likely have controls matched to multiple treatments.

**Common approaches**: 
- **Nearest neighbor matching**: Each unit is matched with the closest control unit based on the propensity score.
- **Caliper matching**: Matches are only made if the propensity score difference is within a specified range (caliper).
- **Exact matching**: Only units with exactly the same propensity score are matched.

### 2) Weighting
Inverse-propensity weighting (IPW) assigns weights to individuals based on their propensity score.  The weight for a treated variable is $\frac{1}{e(X_i)}$, and the weight for a control individual is $\frac{1}{1 - e(X_i)}$.  This reweights the sample to create a pseudo-population where covariates are balanced across treated and untreated groups.

- Uses the entire sample to retain more statistical power
- Allows for the estimation of average treatment effects that apply to the entire population, not just the matched subset.

The Inverse Probability Weighted Estimator (IPWE) is known to be unstable if some estimated propensities are close to 0 or 1.  In such instances, the IPWE is dominated by a small number of subjects with large weights.  To address this issue, smoothed IPW estimators use Rao-Blackwellization which reduces the variance of IPWE by up to 7-fold and can protect the estimator from model misspecification.

Inverse probability weighting is also used to account for missing data when subjects with missing data cannot be included in the primary analysis.
### 3) Stratifying

