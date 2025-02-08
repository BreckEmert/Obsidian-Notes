![[R_stepwise_model_selection_features.xlsx]]

![[Pasted image 20241101123526.png]]

#### Caret implementation
Using cross-validated stepwise:
```r
library(caret)
train.control <- trainControl(method = "cv", number = 5)
simulated.step <- train(y ~ .^2 + I(x1^2) + I(x2^2) + I(x3^2), 
                    data = simulated,
                    method = "lmStepAIC",
                    trControl = train.control,
                    trace = TRUE)
# simulated.step$results
# simulated.step$finalModel
# summary(simulated.step$finalModel)
```

Full implementation of hardcoded formulas.  Done because we were restricted to one quadratic and one interaction term in this case.
```r
library(caret)
library(dplyr)
library(knitr)

# Load the data
simulated <- read.delim("Data/SimDat.tsv", header = TRUE)

# Set up CV and formulas
simulated.trainControl <- trainControl(method = "cv", number = 5)

mainEffects <- c("x1", "x2", "x3")
quadratics <- paste0("I(", mainEffects, "^2)")
interactions <- c("x1:x2", "x1:x3", "x2:x3")

# Loop through each combination, restricted to one interaction and one quadratic term
results <- data.frame(ModelFormula = character(), MSE = numeric(), stringsAsFactors = FALSE)
for (quadratic in quadratics) {
  for (interaction in interactions) {
    formula <- as.formula(paste("y ~ x1 + x2 + x3 +", quadratic, "+", interaction))
    model <- train(formula, data = simulated, method = "lm", 
                   trControl = simulated.trainControl, metric = "RMSE")
    results <- rbind(results, data.frame(ModelFormula = paste(deparse(formula)), 
                                         MSE = round(model$results$RMSE^2, 2)))
  }
}

# Print a sorted table
results <- results %>% arrange(MSE)
results$ModelFormula <- gsub("I\\(([^)]+)\\^2\\)", "\\1^2", results$ModelFormula)

kable(results,
      format = "markdown",
      caption = "Sorted Models by CV MSEs",
      col.names = c("Model Formula", "MSE"), 
      table.attr = 'class="table table-bordered table-striped"') %>%
  kable_styling(full_width=F, position="left")
```
## Problems with stepwise:
- $R^2$ values are biased high
- The $F$ and $c2$ statistics do not have the claimed distribution
- The standard errors of the parameter estimates are too small
	- The confidence intervals around the parameter estimates are too narrow
- The p-values are too low due to multiple comparisons and are difficult to correct
- The parameter estimates are biased high in absolute value
- Collinearity problems are exacerbated
- It encourages you not to think

You might argue that criterion such as AIC provide a theoretical justification for its approximate optimization.  But the AIC is just a restatement of the p-value and you may just be using a different threshold than say $p=0.05$ when you use AIC.

#### Counterarguments
https://statmodeling.stat.columbia.edu/2014/06/02/hate-stepwise-regression/
Larry Wasserman of Carnegie Mellon says:
	I love stepwise regression.  It's a very simple and effective way to do variable selection.  The lasso and stepwise are approximately the same (as shown in the LARS paper by Efron et al).  There are results by Andrew Barron that show that stepwise achieves optimal risk (see "Approximation and Learning By Greedy Algorithms below).  But of course one should not use the output of this (or any selection method) for inference - but for prediction it is great.

The LARS paper is critical of stepwise and finds it to be too greedy, and finds that lasso and stagewise are approximately the same, but have better properties than stepwise regression.  Stagewise takes smaller steps than stepwise, and as such allows multiple collinearity variables into the model in a way that might be better for predictive accuracy.
	Yes stagewise is closer, but in practice, when the dimension is large, Larry finds they're almost always very similar.  There is little practical difference, but stepwise is easier to implement and explain.  The risk bounds are the same as those derived by Greenshtein-Ritov for the lasso - so from that perspective they are the same.

![[learning-by-greedy-algorithms.pdf]]

Stepwise selection *is* data-dredging.  We're finding the model with the most explanatory power even if it makes no sense.  Explanatory power without causal understanding can be dangerous because you don't know when the correlations that make the model work will be broken.  You end up with black swan types of failure modes.  It also becomes unclear how to move forward with improving the model when you don't understand why it works.