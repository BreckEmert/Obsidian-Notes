#flashcards/ML

Naive Bayes
- Why 'naive'?
- Base formula and predictive formula
- Predicts the \_ with the given \_.
?
$$P(C|X) = P(X|C) * P(C) / P(X)$$
for class $C$, features $X$
where:
- $P(C|X)$ is the posterior probability of $C$ given features $X$
- $P(X|C)$ is the likelihood
- $P(C)$ is the prior
- $P(X)$ is the evidence (which is the same for all classes when comparing them)
$$P(X|C) = \prod_{i=1}^n P(X_i|C)$$
for word $x$
So we can ignore normalizing $P(X)$ (it's a *monotonic transformation* so it doesn't change the argmax), to get:
$$\hat{C} = \arg\max_{C} \left[ P(C) \prod_{i=1}^{n} P(X_i \mid C) \right]$$
Uses a known set of data to classify new based on relative counts.  It predicts the class with the highest posterior prob given the input features.  The 'naive' part comes from its assumption that all features are conditionally independent given the class.
<!--SR:!2025-04-02,1,230-->

