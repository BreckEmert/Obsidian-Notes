### Logistic Regression:
```
iris <- read.delim("Data/iris.tsv", header=TRUE)
iris$SpeciesBin <- ifelse(iris$Species == "virginica", 1, 0)

X <- iris$Petal.Width
Y <- iris$SpeciesBin

alpha = 0.005
max_iter = 10000
n <- length(Y)

beta_0 <- 0
beta_1 <- 1

for (i in 1:max_iter) {
  predictions <- beta_0 + beta_1*X
  predicted_probabilities <- 1 / (1 + exp(-predictions))
  
  beta_0_gradient <- sum(Y - predicted_probabilities)
  beta_1_gradient <- sum((Y - predicted_probabilities) * X)
  
  beta_0 <- beta_0 + alpha*beta_0_gradient
  beta_1 <- beta_1 + alpha*beta_1_gradient
}

print(round(beta_0, 2))
print(round(beta_1, 2))
```

### Linear Regression:
```
iris <- read.delim("Data/iris.tsv", header=TRUE)
versicolor <- subset(iris, Species == "versicolor")
virginica <- subset(iris, Species == "virginica")
combined <- rbind(versicolor, virginica)
combined$SpeciesBin <- ifelse(combined$Species == "virginica", 1, 0)

X <- combined$SpeciesBin
Y <- combined$Petal.Width

# alpha <- 5e-10
beta_0 <- 0
beta_1 <- 0

for (i in 1:max_iter) {
  predictions <- beta_0 + beta_1*X
  residuals <- Y - predictions
  
  beta_0_gradient <- 2 * sum(residuals) / n
  beta_1_gradient <- 2 * sum(residuals * X) / n
  
  beta_0 <- beta_0 + alpha*beta_0_gradient
  beta_1 <- beta_1 + alpha*beta_1_gradient
}

print(round(beta_0, 2))
print(round(beta_1, 2))
```