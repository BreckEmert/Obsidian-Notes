## ADD NOTES FROM THIS PAPER
![[ElasticNet.pdf]]

![[Pasted image 20241017203841.png]]

## Lasso (L1) and Ridge (L2)
*Ridge and Lasso do not universally dominate the other, and cross-validation can be used to compare the techniques.*

LASSO and Ridge are super great, but distinct.  Ridge shines at being more stable and reproducible.  A highlight on why lasso doesn't always work is with multicollinearity.  LASSO 'grabs on' to whatever explains the training dataset best, and is more noisy as a result.  But ridge doesn't have this specific goal of excluding features - if a feature is weak, but important, ridge can keep it in better.  If you don't need to interpret coefficients, ridge is often the choice.  And we have the obvious, but rare weakness of p > n, where LASSO can only select n parameters.

But LASSO is very nice compared to Ridge for interpretability.  We get nice, discrete points in the model performance if we look at the specific points where coefficients were driven to zero, as we increased lambda.  This is a random lambda point I got on the homework this week, and it's obviously much nicer to look at than a bunch of near-zero coefficients:
![[LASSO_interpretability.png]]

But the interpretability benefit of LASSO doesn't just stop there.  If you can get what coefficients work, this can impact future work you do.  You may be able to stop collecting, either entirely or just stop pulling variables out of your database, as you update your models.  In the real world, you have to rebuild models to avoid concept drift.  This helps with computation and workflow.  Also, in real life, problems may actually be sparse.  There's a lot of statistical noise as you get variables, and it is quite possible that L1 provides a better fit.
#### Pros/Cons Summary of Lasso:
**pros**:
- Real-world may be sparse.  Especially as $p$ increases, L1 may provide a better fit by reducing noisy correlations.
- In future analyses, the interpretable coefficients may help with workflow.  You can include less variables in future models or collect less data.
**cons**:
- Bad with multicollinearity, as it tends to grab onto whatever explains the training dataset best and shrink colinears to 0.
- (only a con in a certain light) - Only selects $n$ parameters when $p > n$.
	- This can be an upside relative to Ridge, as OLS breaks down when p>n, while Lasso can just use a subset of parameters.
- More sensitive to changes in the training dataset.
#### Pros/Cons Summary of Ridge:
**pros**:
- Handles multicollinearity better.
- More stable and reproducible results with different training datasets.
cons:
- Lacks interpretability - "everything is more or less correlated with everything"

#### "Bet on Sparsity" Principal
https://statmodeling.stat.columbia.edu/2013/12/16/whither-the-bet-on-sparsity-principle-in-a-nonsparse-world/
In optimization, the $l_0$ norm is the number of non-zero elements in a vector, and is good for enforcing sparsity.  But it's NP-hard; finding the exact solution requires checking all possible combinations of non-zero elements, which scales exponentially with the size of the problem.

$l_1$ solvers are really just a relaxation of the actual $l_0$ problem - they serve as a convex approximation of the $l_0$ norm.  While $l_1$ solvers work well in practice, they often leave a nagging feeling that the solver didn't go far enough.