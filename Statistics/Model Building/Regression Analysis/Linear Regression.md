$b_0$ and $b_1$ are calculations based on our sample and are *not* estimated.  What we are estimating is the underlying regression of the population, $B_0$ and $B_1$.

## Proofs
#### Show that
a) $\frac{\partial Q}{\partial b_0} = 0 \rightarrow nb_0 + \sum X_i b_1 = \sum Y_i$
$$
\begin{align*}
Q &= \sum_{i=1}^{n} \left( Y_i - (\beta_0 + \beta_1X_i) \right)^2 \\
Q' &= -2 \sum_{i=1}^{n} \left( Y_i - \beta_0 - \beta_1 X_i \right) = 0 \\
\sum_{i=1}^{n} Y_i &= \sum_{i=1}^{n} \beta_0 + \sum_{i=1}^{n} \beta_1X_i \\
\sum_{i=1}^{n} Y_i &= n b_0 + \sum_{i=1}^{n} X_ib_1
\end{align*}
$$

b) $\frac{\partial Q}{\partial b_1} = 0 \rightarrow \sum X_i b_0 + \sum X_i^2 b_1 = \sum X_i Y_i$
$$
\begin{align}
\frac{\partial Q}{\partial \beta_1} = 
	-2 \sum_{i=1}^{n} X_i(Y_i - \beta_0 - \beta_1 X_i)X_i = 0 \\
\sum_{i=1}^{n} X_iY_i = 
	\sum_{i=1}^{n}X_ib_0 + \sum_{i=1}^{n} X_i^2b_1 \\ 
\sum_{i=1}^{n}X_iY_i = 
	b_0\sum_{i=1}^{n}X_i + b_1\sum_{i=1}^{n}X_i^2
\end{align}
$$


#### Let $Y_i \overset{iid}{\sim} N(\mu_Y, \sigma_Y^2)$.  Show
a) $\frac{\overline{Y} - \mu_Y}{\frac{\sigma_Y}{\sqrt{n}}} \sim N(0, 1)$
b) $\overline{Y}_1 - \overline{Y}_2 \sim N\left( \mu_1 - \mu_2, \frac{\sigma_1^2}{n_1} + \frac{\sigma_2^2}{n_2} \right)$

![[LinearRegression-OLSproof.png]]

Perform least squares using linear algebra in R:
```r
X <- cbind(1, c(1, 2, 4))
y <- c(7, 6, 5)

beta_hat <- solve(t(X) %*% X) %*% t(X) %*% y
beta_hat
```
Perform least squares using single-predictor formula in R:
```r
X <- c(1, 2, 4)
y <- c(7, 6, 5)

beta_1 <- sum((X - mean(X)) * (y - mean(y))) / sum((X - mean(X))^2)
beta_1
```
