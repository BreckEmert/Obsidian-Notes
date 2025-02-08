- **Euclidian distance** $d(x, y) = \sqrt{\sum_{i=1}^{p} (x_i - y_i)^2}$
	- Where $i$ is the $i$-th variable
	- Can be standardized by dividing $x_i - y_i$ by $s_i$
		- This ignores covariances, unlike Mahalanobis
- **Mahalanobis distance** $d(x, \bar{x}) = \sqrt{(x - \bar{x})^\top \mathbf{S}^{-1} (x - \bar{x})}$
	- Also known as statistical distance
	- Accounts for variance and covariance ($S$ is fully known as "variance-covariance" matrix)
	- $d^2$ is a common statistic used to find outliers (positive, chi-squared, non-linear)
- **Determinant**: $ad-bc$, or $(-1)^{i+j})$ pattern for larger matrices (+ - + -)
- **Singular matrix**: determinant is zero, so it doesn't have an inverse
- **Nonsingular matrices** can be inverted which is necessary for Mahalanobis distance
- **Matrix rank**: the number of linearly independent rows or columns in a matrix
	- Also the dimensionality of the column space
	- To calculate: put the matrix in row echelon form and count the minimum of the number of non-zero rows or columns.
	- A square matrix is implied full rank if the determinant is non-zero
- **Positive definite**: $x^TAx>0$ for any non-zero vector $x$
	- These matrices have all positive eigenvalues and are invertible
	- Positive semi-definite is $\geq 0$, indefinite contains either
	- To calculate: get the sign of all eigenvalues 
		- or test leading principal minors (determinants of the top-left submatrices)
- **Eigenvalue ($\lambda$)**: a scalar such that $Av=\lambda v$
- **Eigenvector ($v$): a non-zero vector that only changes in scale after a linear transformation
	- Calculated by solving $\text{det}(A-\lambda I) = 0$, the "characteristic equation"
	- Eigenvectors corresponding to distinct eigenvalues are linearly independent

## Multivariate Statistical Tests
- **One-sample test for mean vector**
	- Tests whether the sample mean vector differs from a known population mean vector
	- One-sample Hotelling's $T^2$ test with assumptions of MVN and no significant outliers
		- $F = \frac{n - p}{p (n - 1)} T^2$, with $p$ and $n-p$ df
	- ***The test used when you have one mean vector and one population mean vector.***
- **Two-sample tests for mean vectors**
	- Compares the mean vectors of two independent groups
		- Compares two mean vectors to each other, rather than to known mean vector
	- **Two-sample Hotelling's $T^2$ test**
		- Assumes MVN and homogeneity of covariance matrices between groups
		- If the assumption of equal covariance matrices holds (based on Box's M test), we can pool the covariance matrices by taking a weighted average based on sample sizes.
		- $F = \frac{(n_1 + n_2 - 2) p}{n_1 + n_2 - p - 1} T^2$, with $p$ and $n_1+n_1-p-1$ df
		- If you reject $H_0$ you can look at individual variables for a difference
	- ***The test used when you have two measured mean vectors.***
- **Paired two-sample test for mean vectors**
	- Tests whether the mean difference between paired observations is zero
		- Equivalent to the one-sample test on the differences (incl. for F-tests)
	- **Paired Hotelling's $T^2$ test
		- $T^2 = n \bar{\mathbf{d}}^\top \mathbf{S}_d^{-1} \bar{\mathbf{d}}$
			- $n$ is the number of pairs
			- $\bar{d}$ is the sample mean difference vector
			- $S_d$ is the sample covariance matrix of the differences
	- ***The test used when you have one group, one response, and repeats.***
- **MANOVA**
	- Tests for differences in mean vectors across multiple groups and/or responses
		- Considers the overall response profile (relationship) between dependent variables
	- Wilks' Lambda, Pillai's Trace, and Hotelling-Lawley Trace use F-tests with $p$ and $n-p$ df
		- These tests involve det/eigenvalues of error matrices and total/hypothesis matrices
		- [[MANOVA]]
	- ***The test used when you have multiple groups and/or responses***
- **Repeated Measures MANOVA**
	- Tests for mean differences in dependent variables that have **more than two repeats**
		- Example: Measuring stiffness and bending strength of boards and multiple time points after applying a preservative.
		- ***The test used when you have more than two repeats***
	- Assumes MVN and sphericity (Mauchly's test)
	- $p$ (# dependent variables) and $(n-g)*(m-1)$ degrees of freedom where:
		- $g$ is the number of groups and $m$ is the number of repeated measures
- **Profile analysis**
	- Profiles are the mean response vectors across groups
	1) tests the parallelism (shape); no interaction between factors
	2) tests levels (flatness); responses are not increasing or decreasing
	3) tests Coincidence (Equality of Profiles), which means the overall mean levels are the same
	- Assumes MVN and homogeneity of variances, and $\min(cell) > num(DVs)$
	- The minimum sample size in each group should be **greater than** the number of dependent variables ($\min(\text{group size}) > \text{num(DVs)}$).
	- $p$ and $n-p$ degrees of freedom in $F$-tests

#### Multivariate normality tests
- Mardia's test for skewness and kurtosis
- Royston's test (an extension of the Shapiro-Wilk test)
- Skewness cutoff is often $\pm0.5$
	- Sources online say $\pm 2$ (1.96?) and this is sourced
	- $\pm 3$ for kurtosis would have the same kurtosis as the normal distribution
	- Use the Jaurque-Bera test instead as it is based on skewness and kurtosis
		- Tests if they match a normal distribution
		- KS test (Kolmogorov-Smernov) has issues when pop. params are not known and is sensitive to large sample sizes
		- Also is Anderson-Darling test?  AD performs better on tails.

QQ Plots
- Upward curve indicates right-skewed data (positive skewness)
- S-shaped curved indicates kurtosis, typically heavy tails
- Can be created using Mahalanobis distances to identify MVN

**High leverage points:**
- Far from the center of predictor space (mean of the independent variables)
- Have strong pull on the regression line because they are in regions with sparse data
	- Think typical explanation of funnel-shaped CI and strength of only predicting the mean
- Identified by a large hat value $h_{ii}$, typically above $2\frac{p}{n}$ or $3\frac{3}{n}$
**High distance points:**
- Large residual - they have an unexpected response given their predictor values
- May be close to the center of predictor space but differ greatly in the outcome
**Influential points are both high leverage and high distance points.**
- Measured using Cook's Distance

#### Equal covariance matrices
- Box's M test can be used to test the covariance matrices' equality
	- This is also heavily subject to MVN and small/large sample sizes
	- Null hypothesis is that all groups have equal covariance matrices
- Visually, the spread of points should be similar across groups
	- Look at the confidence ellipses: shape, orientation, and size

## PCA


## R Code Dump
Outliers testing:
```r
library(GGally)
library(e1071)
library(knitr)
library(kableExtra)

df <- read.csv("../data/table4_6.csv")
ggpairs(df, progress=FALSE)

# Skew and Kurtosis
calculateStats <- function(data) {
  results_df <- data.frame(Variable = character(), Skewness = numeric(), Kurtosis = numeric(), stringsAsFactors = FALSE)
  
  for (var in names(data)) {
    skew <- skewness(data[[var]], na.rm = TRUE)
    kurt <- kurtosis(data[[var]], type=2, na.rm = TRUE) # g_1 \sqrt{n(n-1)} / (n-2)
    
    results_df <- rbind(results_df, c(var, round(skew, 2), round(kurt, 2)))
  }
  
  colnames(results_df) <- c("Variable", "Skewness", "Kurtosis")
  return(results_df)
}
stats_results <- calculateStats(df)
kable(stats_results, format = "html", caption = "Skewness and Kurtosis of Variables") %>%
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive"))


# Mahalanobis distances
mahalanobis_distances <- mahalanobis(df, colMeans(df, na.rm = TRUE), cov(df))

# Test with a chi-square
p_values <- pchisq(mahalanobis_distances, df = ncol(df), lower.tail = FALSE)
outliers <- which(p_values < 0.05)
cat("Outliers:", outliers)


# Univariate boxplot outliers
outliers_data <- data.frame(variable = character(), outlier_index = integer(), outlier_value = numeric(), stringsAsFactors = FALSE)
for (var in names(df)) {
  outliers <- boxplot(df[[var]], plot = FALSE)$out
  if (length(outliers) > 0) {
    outliers_data <- rbind(outliers_data, 
                           data.frame(variable = var, 
                                      index = which(df[[var]] %in% outliers), 
                                      value = outliers))
  }
}
kable(outliers_data)
```

Q: Why and how is the Mahalanobis distance distributed?
A: The data matrix X is a multi-dimensional cloud of n points, shaped by their distributions.  The expression shown, the population Mahalanobis distance formula, measures how far each point $X_i$ is from the center of this cloud, but with additional accounting for covariance.  (Note that this assumes multivariate normality of X)

It is the case that two points far from the mean would have a higher distance with the L2 norm.  However, this doesn't account for covariance: two values with large deviation isn't as unusual when we understand that they correlate with each other.  Mahalanobis distance accounts for this fact by transforming each variable's distribution to have independence and unit variance, accounting for the information about covariance.  Multiplying with the inverse covariance matrix gives us a set of uncorrelated variables with unit variance, making the distribution spherical around the mean. 

However, in this operation, it's not that each variable is transformed into a normal distribution independent of others, it's a special operation that gives us these properties in the new space: variables are uncorrelated and have unit variance.  In this space, distance has the same meaning at any polar coordinate, whereas before, the data was ellipsoidal and the relative value of a distance changed.  We get this from the fact that the inverse of the covariance matrix is made up of the steps to undo the covariance, so the data is made into a space where these covariances have been normalized.

All-in-all, **this process gives data which follows a Chi-squared distribution with p degrees of freedom**.  This is because we have p standardized and independent variables squared by the formula, which is exactly what the chi-squared distribution originally entails.

Q: How is the statistical distance distributed as $n-p$ gets large?
A: We know that our formula has a chi-square distribution when X̄ is distributed as Np(µ, 1/nΣ).  This is equivalent to when sqrt(n)(X̄-µ) has a multivariate normal distribution.  As a result, we can use the sample covariance matrix S for Σ when n is large (n-p is large), as the DoF allows us to estimate the population parameters without the loss of much accuracy.  That is, the law of large numbers lets us converge to the population parameters µ and Σ.  So, the distance remains distributed in a chi-square fashion.