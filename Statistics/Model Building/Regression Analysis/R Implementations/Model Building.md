Complex formula building: this sets up combinations of main effects and higher-order terms, but has the restriction of only allowing one quadratic and one interaction at a time.
```r
library(caret)
library(dplyr)
library(knitr)

# Load the data
simulated <- read.delim("Data/SimDat.tsv", header = TRUE)

# Set up CV
simulated.trainControl <- trainControl(method = "cv", number = 5)

# Set up formulas, restricted to one interaction and one quadratic term
mainEffects <- c("x1", "x2", "x3")
mainEffectCombinations <- unlist(lapply(1:length(mainEffects), function(n) {
  combn(mainEffects, n, simplify = FALSE)
}), recursive = FALSE)

quadratics <- paste0("I(", mainEffects, "^2)")
interactions <- c("x1:x2", "x1:x3", "x2:x3")

# Loop through each formula
results <- data.frame(ModelFormula = character(), MSE = numeric(), stringsAsFactors = FALSE)
for (mainEffectSet in mainEffectCombinations) {
  # Add main effect(s) terms
  formula.temp <- paste("y ~", paste(mainEffectSet, collapse = "+"))
  
  for (quadratic in quadratics) {
    # Skip model if main effects of higher-order term are not in it
    if (!any(mainEffectSet %in% gsub("I\\(([^)]+)\\^2\\)", "\\1", quadratic))) next
    # Add quadratic term if it exists
    if (quadratic != "") {formula.temp <- paste(formula.temp, "+", quadratic)}
    
    for (interaction in interactions) {
      # Skip model if main effects of higher-order term are not in it
      if (!all(unlist(strsplit(interaction, ":")) %in% mainEffectSet)) next
      # Add interaction term if it exists
      if (interaction != "") {formula <- paste(formula.temp, "+", interaction)}
      
      formula <- as.formula(formula)
      model <- train(formula, data = simulated, method = "lm", 
                     trControl = simulated.trainControl, metric = "RMSE")
      results <- rbind(results, data.frame(ModelFormula = paste(deparse(formula)), 
                                           MSE = round(model$results$RMSE^2, 2)))
    }
  }
}

# Print models sorted by MSE
results <- results %>% arrange(MSE)
results$ModelFormula <- gsub("I\\(([^)]+)\\^2\\)", "\\1^2", results$ModelFormula)

kable(results,
      format = "markdown",
      caption = "Sorted Models by CV MSEs",
      col.names = c("Model Formula", "MSE"), 
      table.attr = 'class="table table-bordered table-striped"') %>%
  kable_styling(full_width=F, position="left")
```

Perform least squares on a subset `B` and `C` of categorical `Store`.  This helps with preparing a categorical variable for the model using `dplyr`
```r
q22 <- data.frame(
  Store = c("A", "B", "B", "A", "C", "C", "B", "A", "C"),
  Sales = c(150, 175, 150, 225, 250, 275, 175, 275, 325)
)

q22_prepped <- q22 %>%
  subset(Store %in% c("B", "C")) %>%
  mutate(Store = relevel(factor(Store), ref="B"))
q22_prepped

# Model
q22_prepped.lm <- lm(Sales ~ Store, data=q22_prepped)
summary(q22_prepped.lm)
```