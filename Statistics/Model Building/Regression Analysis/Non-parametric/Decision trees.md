

## Decision trees vs. regression
There are two main points:

1) **Interpretability**: Parametric regression models are typically viewed as the gold standard for interpretability, but still aren't _definite_ in how we can take actions using them.  Decision trees are a series of if-then rules, so they're powerful for things like targeting customers for a promotion since the splits inherently group the data into the most informative categories.  We get a series of splits early on in the tree which clearly divide our data.

In summary, parametric regression focuses on good _inference_, while decision trees focus on good decision making (big surprise, I know).  Though, both methods perform both reasonably well.

2) **Modeling robustness**: Decision trees are in a great middle ground, capable of modeling linear and nonlinear data, and dealing with common problems in datasets.  If a decision tree has to model a single linear predictor, it has to do so like a ladder instead of one clean non-axis aligned split.  But with multivariate data, where interactions may occur along with the base relationships, it doesn't have to explicitly model this. They also can quite often handle outliers, especially if the outlying feature is not an early split.

-   If the relationship between your independent and dependent variables is approximately linear, decision tree will try to approximate it with a ladder like line. Those errors around the steps add up.
-   I would almost always go to a random forest over a decision tree, and just intuitively, it would be especially useful for so many samples. Random forests explore the feature space more thoroughly and then average the results, so they aren’t really a more complex model than decision trees, they are just better.
	- As a general rule though, if the dependencies in the data are linear, then nonlinear models will detect them with less power. It may be that you have a linear generating process and so the linear model performs better.
- biased towards categorical covariates