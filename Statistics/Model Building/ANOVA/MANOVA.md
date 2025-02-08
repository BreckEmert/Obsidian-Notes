MANOVA -> One-Ways -> Pairwise (expand this later)
## MANOVA Test Statistics

Several test statistics are used in MANOVA to assess whether mean vectors differ across groups:

- Wilks' Lambda (Λ)
  - Measures the ratio of the determinant of the error matrix (residual sum of squares) to the determinant of the total matrix (sum of squares and cross products).
  - It evaluates how much variance in the dependent variables is not explained by the group differences.
  - A smaller Wilks’ Λ indicates a greater group effect (more explained variance).

- Pillai’s Trace
  - The sum of the eigenvalues of the matrix product of the hypothesis and error matrices.
  - It’s the most robust statistic, meaning it is less sensitive to violations of assumptions (e.g., unequal variances).
  - Pillai’s Trace assesses the proportion of explained variance in the dependent variables due to the groups.

- Hotelling-Lawley Trace
  - The sum of the eigenvalues of the matrix product of the hypothesis matrix and the inverse of the error matrix.
  - It’s sensitive to large effects and emphasizes strong differences between groups.
  - This trace often leads to a more powerful test when the group differences are substantial.

- Roy’s Largest Root (Roy’s Maximum Root)
  - The largest eigenvalue of the matrix product of the hypothesis and error matrices.
  - Focuses only on the maximum variance explained by any single discriminant function.
  - This test is very sensitive to the strongest single effect but might overlook smaller effects.

### Quick Summary:
- Wilks’ Λ: Tests the proportion of unexplained variance (smaller value = greater effect).
- Pillai’s Trace: Robust test; considers the total explained variance.
- Hotelling-Lawley Trace: Emphasizes large group differences; powerful for strong effects.
- Roy’s Largest Root: Sensitive to the largest single effect; might miss smaller effects.
