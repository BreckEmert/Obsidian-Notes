## Matching
The goal of matching is to reduce model dependence.

#### Example
In this example, we're highlighting the what the effects of the binary causal variable (treatment vs. control) can be on what model fits well.  Education is just a control variable, so we're **finding the effects of the binary causal variable on the outcome holding constant education**.
![[CausalInference-ModelDependence.png]]

Normally we'd run a regression of the outcome on the outcome of the binary variable (that's the key causal variable that we want to know the effect of, C vs. T), controlling for education.  If you run that regression, this is what you get:
![[CausalInference-ModelDependence-Regression.png]]
We see that the effect is **positive**, and goes up from control to treatment.  You notice this lack of fit as a researcher and say "maybe we should add a quadratic term".  Now we'll run a regression of the outcome variable on T vs. C, education *and* education squared:
![[CausalInference-ModelDependence-QuadraticRegression.png]]

What we've found here is **model dependence**.  A small change in the specification produced big changes in the subsequent results.  **Model dependence is the variation in your causal effects across all of the results you (can) try.**

#### Matching should have been called pruning, because that's what it does.  It deletes observations in a very specific way.

After matching, we get these results:
![[CausalInference-Matching.png]]
Notice that the quadratic and the linear models are almost identical.  It does not matter, in the matched data, whether we run a linear or quadratic regression; that model dependence has gone away.

#### Without matching:
- Imbalance.  Matching is trying to get rid of the relationship between the control variables and the key causal variable.
	- Imbalance is the source of model dependence.  When there is imbalance, when you specify your model, you'll get different results.
	- This leads to researcher discretion.  We know this is bad.  We will inadvertently or intentional bias the results in our favor.
	- **This leads to bias**.
	- Qualitative choice from unbiased estimates = biased estimator.
		- e.g. choosing from results of 50 randomized experiments.  Every one of these is unbiased, judged ahead of time.  But choosing one based upon the results means that we have a biased answer.
		- It doesn't matter that each experiment was, even if we had a whole lot of unbiased estimates.
		- Choosing based on "plausibility" is probably worse.
			- Conscientious effort doesn't avoid bias (Banaji 2013).  People don't have easy access to their own mental processes or feedback to avoid the problem.

#### With matching:
- No imbalance $\leadsto$ no model dependence $\leadsto$ no researcher discretion $\leadsto$ no bias

### What's matching?
- $Y_i$ dep var, $T_i$ (1=treated, 0=control), $X_i$ confounders
- **Treatment Effect for *treated* observation $i$:**
$$
\begin{align}
\text{TE}_i & = Y_i(1) - Y_i(0) \\
& = \text{observed} - \text{unobserved}
\end{align}
$$
- In words, the treatment effect is the value of y if it were treated (which it is) minus the effect if it were not treated.
- **Estimate $Y_i(0)$ with $Y_j$ with a matched ($X_i \approx X_j$) control.**
	- Matching says "let's estimate the unobserved thing with another observation which is very much alike the observation we're studying.  In fact, it matches on all of the control variables."
	- We'd like to match on all the control variables, but if we can't match exactly we'll match very well.  We'll just take the outcome variable value (which is $Y_j$) and substitute it in as the way of estimating the treatment effect.
- **Quantities of interest**:
	- SATT: Sample Average Treatment Effect on the Treated:
$$\text{SATT} = \underset{i \in \{T_i = 1\}}{\text{Mean}} \left( \text{TE}_i \right)$$
	- FSATT: Feasible SATT (prune badly matched treateds too)
- **Big convenience of matching**: Follow preprocessing with whatever statistical method you'd have used without matching.
- **Pruning nonmatches makes control vars matter less**: reduces imbalance, model dependence, researcher discretion, and bias.  It *breaks the link between the control variable and the X.*

**Matching finds hidden randomized experiments.**
![[CausalInference-BalanceTable.png]]

**Goal of each matching method (in observational data):
- PSM: complete randomization
- Other methods: fully blocked
- Other matching methods dominate PSM

### Method 1: Mahalanobis Distance Matching
Approximates a fully blocked experiment.

1. Preprocess (matching)
	- $\text{Distance}(X_c, X_t) = \sqrt{(X_c - X_t)'S^{-1}(X_c - X_t)}$
	- Mahalanobis is for methodologists - in applications, use Euclidean!
		- Throws back to the 'correlation matrix brutalization' idea of PCA.
		- The point of standardization is to make your data invariant to the substance.  This isn't a good thing.  It's to make your data invariant to you, and everything you know.
	- Match each treated unit to the nearest control unit.
	- Control units: not reused; pruned if unused
	- Prune matches if distance>caliper (many adjustment methods available)
![[CausalInference-MahalanobisMatching.png]]
In the best case, we end up with this beautiful dataset where the two control variables `Education` and `Age` have no effect on whether we're choosing a `T` or a `C`.  We can put in cubic terms, square terms, we're gonna get the same answer no matter what.
![[CausalInference-MahalanobisMatching-BestCase.png]]

### Method 2: Coarsened Exact Matching
Approximates a fully blocked experiment.

1. Preprocess (matching)
	- Temporarily coarsen X as much as you're willing
		- e.g. Education year into grade school, high school, college and graduate
	- Apply exact matching to the coarsened X, C(X)
		- Sort observations into strata, each with unique values of C(X)
		- Prune any stratum with 0 treated or 0 control units
		- Pass on original (coarsened) units except those pruned
- Estimation (difference in means or model)
	- Weight controls in each stratum to equal treateds

![[CausalInference-CoarsenedExactMatching-Bins.png]]
![[CausalInference-CoarsenedExactMatching-FinalDataset.png]]

![[CausalInference-CoarsenedExactMatching-BestCase1.png]]
![[CausalInference-CoarsenedExactMatching-BestCase2.png]]

### Method 3: Propensity Score Matching
Approximates a completely randomized experiment (not the better fully blocked experiment)

1. Preprocess (matching)
	- Reduce $k$ elements of $X$ to scalar
		- $\pi_i \equiv \Pr(T_i = 1|X) = \frac{1}{1+e^{-X_i\beta}}$
		- This is running a logit (not the one we're interested in) where the treatment variable is temporarily the dependent variable, and all of our control variables are temporarily the explanatory variable.
		- We find $\pi$, the probability of treatment.
	- $\text{Distance}(X_c, X_t) = |{\pi_c - \pi_t}|$
		- Measuring distance from one observation to another *only with respect to* the absolute difference between the control value and the treated value.
	- Match each treated unit to the nearest control unit
	- Control units: not reused; pruned if unused
	- Prune matches if distance>caliper
		- Many adjustments available to this basic method
2. Estimation (difference in means or a model)

![[CausalInference-PropensityScoreMatching-Projection.png]]
![[CausalInference-PropensityScoreMatching-Matching.png]]
![[CausalInference-PropensityScoreMatching-FinalDataset.png]]

We threw away data - we started with two dimensions and made decisions on one dimension.

In the best case, where all propensity scores are the same, the only way to choose data is at random.  **It is at random**, here with probability 0.25.
![[CausalInference-PropensityScoreMatching-BestCaseProjection.png]]
![[CausalInference-PropensityScoreMatching-BestCaseFinalDataset.png]]
Now, **there is no systematic difference between treatment and controls**.  But there is a lot of information left on the table.  Most of the treated units are not on top of the controls.  We did what we set out to do - this is what it looks like if we flipped coins to determine if each unit was treated or control.

The formal problem with this:
### Random pruning increases imbalance
Deleting data only helps if you're careful!

- Random pruning: pruning process is independent of $X$
- **Discrete example**
	- Sex-balanced dataset: treateds $M_t, F_t$, controls $M_c, F_c$
	- Randomly prune 1 treated and 1 control $\leadsto$ 4 possible datasets:
		- 2 balanced $\{M_t, M_c\}, \{F_t, F_c\}$
		- 2 imbalanced $\{M_t, M_c\}, \{F_t, F_c\}$
		- This could achieve a balanced dataset, with $\Pr=0.50$ in this case.  But half of the datasets are imbalanced.
	- $\Rightarrow$ random pruning increases imbalance
- **Continuous example**
	- Dataset: $T \exists \{0, 1\} randomly assigned; $X$ any fixed variable; with $n$ units.
	- Measure of imbalance: squared difference in means $d^2$, where $d = \bar{X}_t - \bar{X}_c$
	- $E(d^2) = V(d) \propto 1/n$ (note: $E(d) = 0$)
	- Random pruning $\leadsto$ $n$ declines $\leadsto$ $E(d^2)$ increases
	- $\Rightarrow$ random pruning increases imbalance
- Fun example:
	- You have a room full of people
	- Think about how far you are from the nearest person
	- Remove half of the people
	- Ask again - are you closer or farther from someone?

#### PSM's statistical properties
1. Low standards: sometimes helps, never optimizes.
	- Efficient relative to complete randomization, but
	- Inefficient relative to (the more powerful) full blocking
	- Other methods dominate:
		- $X_c = X_t \Rightarrow \pi_c = \pi_t$
			- Trying to match treated and control variables exactly.
			- If they do match exactly, that implies that the propensity scores match exactly
		- $\pi_c = \pi_t \not\Rightarrow X_c = X_t$
			- If you start with the propensity scores, this does not imply that the control variables match exactly.
2. **The PSM Paradox**: When you do "better", you do worse
	- When PSM approximates complete randomization (to begin with, or after some pruning) $\leadsto$ all $\hat{pi} \approx 0.5$ (or constant within strata) $\leadsto$ pruning at random $\leadsto$ Imbalance $\leadsto$ Inefficiency $\leadsto$ Model dependence $\leadsto$ Bias
	- If the data have no good matches, the paradox won't be a problem but you're cooked anyway (it will reduce imbalance but you have no good matches)
	- Doesn't PSM solve the curse of dimensionality problem? (because it projects down to one dimension and matches on that)
		- No.  The PSM paradox gets worse with more covariates
	- What if I match on a few important covariates and then use PSM?
		- The low standards will be raised some, but the PSM paradox will kick in earlier (because you've already come close to complete randomization).

#### PSM is blind where other methods can see
This dataset has two control variables, $X1$ and $X2$.  They flipped counts as to whether each observation should be treatment or control.
![[CausalInference-PSM-BlindExample.png]]

This analysis was ran 1000 times, with each simulation shown on the Y-axis.  These are color coded to the image above.
- We see that Mahalanobis does a good job pruning the worst, then the next worst, then the next worse.
- But Propensity Score prunes the worst, and then does a poor job removing the completely randomized experiment.
![[CausalInference-PSMvsMDM-OrderofDrops.png]]

In this example, the blue square represents the possible range where controls were drawn from, and the red for treated examples.  The overlapping region is a completely randomized experiment.
- MDM shows good performance by the darker lines being tossed first, the ones outside of the completely randomized experiment.
- PSM performs relatively randomly at throwing out examples after it throws out the examples that are outside.  The examples inside are thrown out at random.
![[CausalInference-PSMvsMDM-OrderofMatches2.png]]

This graph shows PSM getting better until it approximates complete randomization, and then getting rapidly worse.
![[CausalInference-PSMvsMDM-DependenceAndBias.png]]
The delta $\Delta$ below shows $\text{calipter} = \frac{1}{4} \text{SD}$ which is a common recommend cutoff, but even this is worse than it began with.
![[CausalInference-PSMvsMDM-DependenceAndBias2.png]]

## Conclusions

### Why propensity scores should not be used for matching
- **Low Standards**: sometimes helps, never optimizes.
- **The PSM Paradox**: When you do “better,” you do worse.
- **Some mistakes with PSM**: 
  - Controlling for irrelevant covariates.
  - Adjusting experimental data.
  - Reestimating propensity score after eliminating noncommon support.
  - 1/4 caliper on propensity score.
  - Not switching to other methods.

### A warning for any matching method:
- Pruning discards information; you must overcome this.
- Other methods can generate a “paradox” if you prune after approximating full blocking (rare, but possible).
- If you’re not doing positive good, you may be hurting yourself.

### Matching methods still highly recommended; choose one with higher standards


## Q&A

Question: Does this information translate to other use-cases of propensity scores?
Answer: No.  There are plenty other good uses of propensity scores, but matching is not one of them.  Maybe even weighting is OK but they didn't research that.

Question: You said to automate procedures as much as possible, but then said you shouldn't standardize data.  Those seem to be in tension.
Answer: You want to take away from the analyst anything they don't know.  But if they know something, and can contribute something, then they should.

Question: If we do an RCT, why would we do matching and pruning afterwards?
Answer: Ideally, if you're running a RCE, you'd do matched pairs.  You would think of all the variables to match on, and match on those variables.  If you do that, there's nothing to match after the fact.  But sometimes you can't do that.  If someone comes in with an injury, you can't wait around until someone else comes in with an injury that looks like theirs.  Although you have no bias ex-ante, you have bias ex-post.

Question: Can you say anything about the observations thrown out?  Any comment on one-to-many matching?
Answer: In general you want to do one-to-many matching, you shouldn't throw away examples if you don't have to.  Really, it's the number remaining that matters.  It doesn't matter how many you throw away.  If you throw away 900,000 examples and still have 100,000, you'll have a small SE and be happy.

Question: If I select health insurance *because* I think I will use it more (endogeneity), does matching have any way help here?
There's nothing in matching that deals with endogeneity.  In matching, you can always try to control for a better set of variables.   But if you're convinced you have an endogeneity problem, matching doesn't solve it.  As an analyst you should generally try to pick a better problem (haha).