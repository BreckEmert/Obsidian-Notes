https://www.youtube.com/watch?v=qlPs8Ioa56Y

## If you treat some elements as missing: make sure they exist
If some values don't exist, what analysis would you do?
- Code the variable as a missingness indicator and predict it
- This analyzes what subjects have the missing data

**Missing completely at random** should probably be called missing at random, 
**Missing at random** should probably be called missing at random conditional on the explanatory variables

| Assumption        | Can predict M with    |
| ----------------- | --------------------- |
| MCAR              | -                     |
| MAR               | $D_{obs}$             |
| NI (Nonignorable) | $D_{obs}$ & $D_{mis}$ |
where $D$ is the dataset

**MCAR (naive)**: the determination of answering survey question is random.  It's just a function of randomness so we can drop $D$:
$$P(M|D) = P(M)$$
**MAR (empirical)**: missingness is a function of measured variables.  The true values of the missing data are not necessary to predict which people answer which questions and which questions are answered by which people, so we drop $D_{mis}$
$$P(M|D) \equiv P(M|D_{obs}, D_{mis}) = P(M|D_{obs})$$
- e.g., Independents are less likely to answer vote choice question (with partisan identification measured (and used as a predictor))
- e.g., Some occupations are less likely to answer the income question (with occupation measured (and used as a predictor))

**NI (fatalistic)**: missingness depends on unobservables.
- $P(M|D)$ doesn't simplify
- e.g., censoring income if income > $100K *and* you can't predict high income with other measured variables
- Adding variables to predict income can change NI to MAR


## Multiple Imputation
Point estimates: consistent, efficient; SEs are right (or conservative)

Impute $m$ values for each missing element
- $m$ is bigger with more uncertainty and more missingness
- Imputation method assumes MAR
- Model must include all (estimational, fundamental) uncertainty
- Produces independent imputations
Create $m$ *completed* datasets
- Observed data are the same across the datasets
- Imputations of missing data differ
	- Cells we predict well don't differ much
	- Cells we can't predict well differ a lot
Run whatever statistical method you would have with no missing data, for each completed dataset

#### Combining analyses from separate imputed datasets
1) By averaging (simple, easy to understand)
	- Point estimate: average individual point estimates $Q_j (j = 1, \dots, m)$  $$\hat{q} = \frac{1}{m}\sum_{j=1}^{m}q_j$$
	- Standard error: $$\begin{align} SE(q)^2 & = \text{mean}(\text{SE}_j^2) + \text{variance}(q_j)(1 + 1/m) \\ & = within + between \end{align}$$
		- $(1 + 1/m)$ is a bias correction for small samples that vanishes as $m$ increases.
2) By simulation (simpler, fits with our procedures)
	- draw $1/m$ of needed sims of the QOI from each dataset
	- combine (i.e., concatenate into a larger set of QOI simulations)
	- make inferences from combined sims as usual
	- Another way of saying this is that we're using mixed-and-matched simulated datasets instead of the original simulated set, but it's still regular MI that you know.
	- Helps capture variability better than the default set.

