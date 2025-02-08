
#### Introduction
A chi-square with $k$ degrees of freedom is the distribution of the SS (sum of squares) of $k$ independent standard normal random variables.
- Pulling $1$ time from $N(0, 1)$ results in a distribution which is initially likely to be near 0, but shifts with $k$ as $\mu=k$, $\sigma^2=2k$, becoming more symmetric and less right-skewed ([[#Probability Density Function]].
- Initially, the curve is mostly near 0, because 66.7% of our values are expected to be within 1, and numbers less than 1 get smaller when squared.
	- At $k=0$, the distribution is *degenerate*, meaning only involving a single outcome $0$.
- The mode ($\chi^2\prime = 0$) grows at $\max(0, k-2$).

The chi-squared distribution $\chi_k^2$ is a special case (reparameterization) of the [[Gamma Distribution]].

#### Definition
If $Z_1,\dots,Z_k$ are independent, standard normal random variables then the sum of their squares
   $Q = \Sigma{i=1}{k}Z_i^2$
is distributed according to the chi-squared distribution with k degrees of freedom, denoted as
   $Q ~ \chi^2(k)$ or $Q ~ \chi_k^2$
The chi-squared distribution has one parameter: a positive integer $k$ that specifies the number of degrees of freedom, the number of random variables being summed, $Z_i s$.
#### Probability Density Function
![[chi-square_pdf.jpg]]

#### Cumulative Distribution Function
![[Chi-square_cdf.jpg]]

