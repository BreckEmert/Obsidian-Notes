https://www.youtube.com/watch?v=9Uxr3Y-zJIY
[Mikko Rönkkö](https://www.youtube.com/@mronkko)

![[Choosing a missing data technique - YouTube - Google Chrome 8_30_2024 3_35_58 PM.png]]

Always go for FIML or MI.  But step 1, is you may be able to demonstrate that missingness is not a big problem in your study.  If you have 1000 observations, and 10 missing cases, that's 1% of your data and maybe a not a big concern.  There, you can apply listwise deletion.  But in no cases should you apply pairwise or single imputation techniques.

Which technique to use (FIML or MI)?
- Both are based on the same statistical theory and are asymptotically equivalent, so in large samples it's a matter of preference.
- Both can use auxiliary variables.  You can enter variables that help in the imputation that are not part of the actual model, but it may be easier to do this with MI than FIML.
- MI is more general, FIML needs to be implemented in each analysis.  FIML needs to be implemented for the particular model you want to use, in your statistical software.  In practice FIML tend to be implemented only instructionally.  It's not all models that work with FIML.
- MI must be as or more complex than the original model.  If you impute variables linearly but calculate an interaction model or a logistic regression model, that is not consistent.  Any feature of the data that you could possibly be interested in needs to be included in the imputation model.  As such, you need to rerun your imputation datasets any time you realize things should be included in the model.  It may be toggling a button and very simple to enable missing data estimation.
- FIML has less convergence issues; MI tend to not always converge.  There is no imputation step that could produce autocorrelations that are undesirable.  In practice, almost always, Mikko goes for FIML.  But this has to do with what questions you're asking.  If you're working with non-linear models, interactions, MI might be better.  When FIML is capable, use it, but use MI when capability runs out.  It takes more time and is more error-prone.