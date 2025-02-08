Visualizing two categorical variables requires `interactions::cat_plot`, but we'll let it figure that out itself to keep formatting consistent:
```r
clinic.interaction <- lm(cholesterol ~ patient*run, data=clinic)  # patient and run are categorical predictors
interactions::interact_plot(clinic.interaction, pred=run, modx=patient)
```