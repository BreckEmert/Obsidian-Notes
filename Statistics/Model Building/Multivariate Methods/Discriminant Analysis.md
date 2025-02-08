#### Preemptive notes on LDA vs. Logistic Regression:
![[pohar-lda-lr.pdf]]
Logistic regression is not intended to be used as a classifier but rather to estimate probabilities.  Classification without an explicit and appropriate utility function is arbitrary and inconsistent with optimum decision making.

Tldr; LDA is the better choice if the population is normally distributed.  In practice, this assumption is nearly always violated.


- LDA does not differ from LR in functional form, only in the estimation of coefficients.
![[LDA-LR-differ.png]]
- The errors LDA makes in prediction are only due to the errors in estimation of the mean and variance of the sample.
- As the estimates for LR are obtained by maximum likelihood, they have a number of nice asymptotic properties.

To illustrate the above conclusions we can take a look at two uniformly distributed explanatory variables shown on the Figure 7a. The boundary between the two groups is obvious and linear. LR therefore finds a straight line that perfectly discriminates the data (7b). The LDA, however, again assumes multivariate normal distribution in both groups – the ellipses in Figure 7c show the assumed normal distribution, calculated on the basis of the means and variances of the two groups. The linear boundary that follows from these calculations, of course, is not optimal.
![[lr-lda-uniform.png]]


## Linear Discriminant Analysis (LDA)

The goal is to model the posterior probability $\Pr(y=k \mid x=x_0)$, where $k$ represents a specific class and $x_0$ represents a data point.  We want a to find these class-conditional PDFs, $f_k(x_0)$, because they allow us to know the probability of the features $x_0$ given that the class is $k$.  When we have the likelihood of each class and combine it with prior probabilities $\pi_k$, we obtain posterior probabilities using Bayes' rule.
$$
\Pr(y = k \mid x = x_0) = \frac{f_k(x_0) \pi_k}{\sum_{j=1}^{K} f_j(x_0) \pi_j}
$$
**The parametric approach in Discriminant Analysis makes the assumption:**
- $f_k$ is multivariate gaussian
$$
f_k(x) = \frac{1}{(2\pi)^{d/2} |\Sigma|^{1/2}} \exp \left( -\frac{1}{2} (x - \mu_k)^T \Sigma^{-1} (x - \mu_k) \right)
$$
**And LDA takes this further to specify a linear decision boundary:**
- All classes share the same covariance matrix $\Sigma$
- We make this assumption because this **simplifies the problem to finding linear decision boundaries** between classes.  When working through the math, taking the log of the likelihood ratio for two classes with the same $\Sigma$ causes the quadratic terms to cancel out.


## Simplified introduction
For the purposes of introduction, let's also assume that we have two classes $y = \{0, 1\}$.

What is the decision boundary?
- A set of points such that the probability of being in one class is the same as being in the other class, written formally as:
$$\text{D.B.} = \{x \mid P(y=1 \mid x=\hat{x}) \; = \; \Pr{(y=0 \mid x= \hat{x})}\}$$
- So, we can 'simply' solve for
$$\Pr(y=1 \mid x=\hat{x}) \; = \; \Pr(y=0 \mid x=\hat{x})$$


## LDA vs. Logistic Regression
Something robust, and good to know, is exactly _why_ the assumptions LDA makes helps us out.  In LDA, we don't _just_ 'assume it' for validity, we're actually _imposing_ this structure on the data.  I think this graph shows best how heavily having to do a simple calculation of the covariance matrix is, by showing we define the LD largely in terms of the inverse covariance matrix.
![[lda decision bound vs first pc.png]]

If the assumptions of LDA are fully met then the errors LDA makes in prediction are only due to the errors in estimation of the mean and variance of the sample.  Inversely, this means we expect LR to perform just as well as LDA when the sample size is high.

For more context, this is what it looks like when imposing structure fails - if you take a look at the data it is clearly uniformly distributed, but you can see the Mahalanobis confidence ellipse around it is clearly not matching this.
![[Pasted image 20241018131711.png]]

We can do any of the following to distinguish the performance of the functions.  All of the simulation proofs and some visualization are in the PDF at the beginning of this note:
1) Lower sample size
	- LDA assumes normality and the errors it makes in prediction are only due to the errors in estimation of the mean and variance on the sample.
	- So, as sample size increases, LR's lack of assumptions start to outperform LDA.  LDA assumes normality even when it's not statistically significant within the sample, and benefits from this.
2) Increase the number of variables
3) Add a shared covariance matrix
4) Increase covariance (though this effect is hardly noticeable)
5) Tighten the class means, LR starts to outperform LDA with higher distances.