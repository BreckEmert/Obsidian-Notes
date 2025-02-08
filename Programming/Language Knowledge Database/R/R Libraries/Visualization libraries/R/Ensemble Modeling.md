```r
library(caret)
library(caretEnsemble)
library(caTools)
library(dplyr)

# Load data
data <- read.csv("Data/dataR2.csv")
data$Classification <- factor(data$Classification, levels=c("1","2"))
data$Classification <- make.names(data$Classification)

# Log-transform skewed cols
data <- data %>%
  mutate(
    glucose = log(Glucose),
    insulin = log(Insulin),
    homa = log(HOMA),
    leptin = log(Leptin),
    adiponectin = log(Adiponectin),
    resistin = log(Resistin),
    mcp1 = log(MCP.1)
  )

# F1 loss function
f1Score <- function(data, lev=NULL, model=NULL) {
  precision <- posPredValue(data$pred, data$obs, positive=lev[2])
  recall <- sensitivity(data$pred, data$obs, positive=lev[2])
  F1 <- ifelse((precision+recall)==0, 0, 2*(precision*recall/(precision+recall)))
  names(F1) <- "F1"
  F1
}

# Train control
train_control <- trainControl(
  method = "cv", 
  number = 10, 
  savePredictions = "final", 
  classProbs = TRUE, 
  summaryFunction = f1Score, 
  sampling = "rose", 
  verboseIter = FALSE
)


# Model list
model_list <- caretList(
  Classification ~ .,
  data = data,
  trControl = train_control,
  metric = "F1",
  preProcess = c("center", "scale"),
  tuneLength = 5,
  tuneList = list(
    rf =        caretModelSpec(method = "rf"),
    glm =       caretModelSpec(method = "glm"),
    svmRadial = caretModelSpec(method = "svmRadial"),
    gbm =       caretModelSpec(method = "gbm", verbose=FALSE),
    glmnet =    caretModelSpec(method = "glmnet"),
    knn =       caretModelSpec(method = "knn")
  )
)

# Train
stacked_model <- caretStack(
  model_list, 
  method = "rf", # names(getModelInfo())
  metric = "F1", 
  trControl = train_control, 
  tuneLength = 5
)


# Output
(stacked_model$ens_model$results)
(head(stacked_model$ens_model$resample))
lapply(model_list, function(model) model$results)

varImp(stacked_model$ens_model)  # Variable importance

mean_f1_score <- mean(stacked_model$ens_model$resample$F1, na.rm=TRUE)
cat("Mean F1 score across folds: ", mean_f1_score)





# Final model selection and hyperparameters

# Get the best models from the previous run
best_specs <- lapply(model_list, function(model) model$bestTune)

final_tune_list <- lapply(names(model_list), function(model_name) {
  caretModelSpec(
    method = model_list[[model_name]]$method,
    tuneGrid = best_specs[[model_name]]
  )
})


# Model list
final_model_list <- caretList(
  Classification ~ ., 
  data = data, 
  trControl = train_control, 
  metric = "F1", 
  preProcess = c("center", "scale"), 
  tuneList = final_tune_list
)

# Train
final_stacked_model <- caretStack(
  final_model_list, 
  method = "xgbTree", 
  metric = "F1", 
  trControl = train_control, 
  tuneLength = 5, 
  verbose = FALSE
)


# Output
(final_stacked_model$ens_model$results)
(head(final_stacked_model$ens_model$resample))

varImp(final_stacked_model$ens_model)  # Variable importance

mean_f1_score <- mean(final_stacked_model$ens_model$resample$F1, na.rm = TRUE)
cat("Final mean F1 score across folds: ", mean_f1_score)

```