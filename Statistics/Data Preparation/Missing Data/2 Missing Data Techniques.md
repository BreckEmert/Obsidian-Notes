https://www.youtube.com/watch?v=RhEuyHr2mWw
[Mikko Rönkkö](https://www.youtube.com/@mronkko)

## Part 1: Summary
Modern techniques are generally preferred, whereas the older and more simpler techniques are overshadowed and lead to issues.

Example 1 - Job Performance
![[Simple techniques for dealing with missing data - YouTube - Google Chrome 8_30_2024 2_43_17 PM.png]]

![[Simple techniques for dealing with missing data - YouTube - Google Chrome 8_30_2024 2_47_46 PM.png]]

**Listwise deletion** - If you must delete observations, this is a better alternative than using pairwise deletion.  Involves deleting entire rows where data is missing.
**~~Pairwise deletion** - perform the correlation matrix by using the complete rows for the specific correlations.  In the photo, this means correlating x2 and x3 with the 1st and 4th observation.~~  Problematic, because it can produce impossible correlation matrices, which may produce errors in the software.  We also want to report sample sizes, but the sample sizes will certainly vary here.  Using the correlation matrix in other analyses, how do we know which sample size to use?  Lastly, even if we can overcome those problems, if we apply the same data in a regression analysis it will then use listwise deletion.  At the very least, this is confusing.

**Mean imputation** - here, all IQs below 100 will have a job performance of 12.  The original regression analysis wouldn't have a problem, but the means are going to be systematically higher because the missingness depends on the X variable.  The variation in job performance will be greatly underestimated.  This is a really bad idea, but very easy to apply so it happens a lot.  Do NOT mean impute, it's the worst thing you can do with missing data.
**Smarter way** - regression-based imputation.  Run a regression analysis on the data we have, and use that model to predict the missing values of job performance.  Results in overestimated correlation and R2 value, because the variation is not modeled.  But the regression coefficient is consistent.
![[Simple techniques for dealing with missing data - YouTube - Google Chrome 8_30_2024 2_58_32 PM.png]]
**Even smarter way** - stochastic regression imputation.  We calculate the predicted values and add random noise to those predictions based on the model.  Estimates the regression line correctly, the R2 correctly, and the mean of job performance correctly.  Still has some problems.  The SEs are biased, because the imputations are not the real data.
![[Simple techniques for dealing with missing data - YouTube - Google Chrome 8_30_2024 3_04_02 PM.png]]

**Simulation of these methods** - things that are bad are highlighted in yellow.  LD = listwise deletion, AMI = arithmetic mean imputation, SRI = Stochastic regression imputation.
We can see that MCAR is pretty OK, MAR has quite a bit of problems, but MNAR is mostly worthless.  We estimate the mean and variance correctly of the independent variable, but this is only because we observed those variables and statistics.
![[Simple techniques for dealing with missing data - YouTube - Google Chrome 8_30_2024 3_08_57 PM.png]]

**Other traditional techniques**
- General techniques:
	- Hot-deck imputation and
	- Similar response pattern imputation - try to find cases that are similar to the missing case, and take values from those to impute.  Same problems with regression-based imputation.
- Scale-item techniques:
	- Averaging available items - sometimes useful, has limitations.
- Time series data:
	- Last observation carried forward - carry value from 2008 into 2009.  Distorts trends but defensible if the amount of missing data is very small.
	- Time series interpolation (basically regression imputation).  Just averages the before and after value.  Same issues as regression-based imputation.

Summary of traditional techniques
- Listwise deletion on MAR is only OK if the missingness is on the X variables.
![[Simple techniques for dealing with missing data - YouTube - Google Chrome 8_30_2024 3_24_13 PM.png]]

#### Why is imputing OK?
It is not 'making up' data:
- The goal of a statistical analysis is to estimate the population parameters.  Multiple imputation is nothing more than a mathematical tool to facilitate this task.  Imputation itself is ancillary to the end goal.
- Multiple imputation and ML estimation are asymptotically equivalent and tend to produce the same results.  The fact that they are interchangeable underscores the point that imputation is not inherently problematic.
- Unlike other imputation routines, multiple imputation explicitly accounts for the uncertainty associated with the missing data.  By repeatedly filling in the data, multiple imputation yields parameter estimates that average over a number of plausible replacement values, so the process never places faith in a single set of imputations.

All in all, we aim to make keep the statistics of our model unbiased.  The estimates and their SEs remain unbiased, and the mean and variance of the features remain unchanged, as well as the statistics we make from them, like the covariance and correlation matrices.
### Using MI and FIML produces *better* results than not.


## Part 2: Implementing Modern Techniques

### Multiple Imputation
![[Multiple imputation - YouTube - Google Chrome 8_30_2024 8_30_02 PM.png]]

When we impute data, these are not actually informative values we care about.  Instead, they are mere samples from *distributions* that we are interested in (see bottom right of above image).  We are trying to model that the data are distributed along the predicted line, but for practical reasons we need to estimate the individual points.  Multiple imputation solves this by estimating them many, many times.

We estimate 20-100 datasets (as many as you can, at least 100 if it's not an unfeasible amount of time.  You can play with this to see the exact impact).  Estimate the mean of those datasets, and then calculate the standard error based on the variance of the estimates, between and within-estimates.  $V_T = V_W + V_B + \frac{V_B}{m}$

![[Multiple imputation - YouTube - Google Chrome 8_30_2024 8_48_13 PM.png]]

Once you perform MI, statistics that quantify uncertainty are no longer valid in their regular form, outside of the pooling procedure.  You must perform post-estimation things like likelihood ratio comparison within the pooling procedure.
### Full Information Maximum Likelihood - FIML

As long as the cause of missingness is observed, MAR, the estimates are consistent and unbiased (asymptotically).  Go observation by observation and calculate observation-level likelihood (in practice, we calculate this for groups of observations with the same missingness pattern).  Instead of comparing two matrices and comparing if one matrix is another one, we calculate how likely one case would be *given* this covariance matrix.
![[Captures 8_30_2024 9_44_08 PM.png]]

- If your data are not normal, you can use robust SEs.
- Make sure your SEs are based on the observed information instead of the expected information.  IT's generally the default, but it's good to check.

### KNN
![[Pasted image 20240911165340.png]]
![[missingness rate vs relative bias.png]]
