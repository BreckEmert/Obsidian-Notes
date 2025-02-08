Stata is a good tool to check, even as a non-Stata user, for the latest information on this process.
![[Workflow for multiple imputation analysis - YouTube - Google Chrome 8_30_2024 9_48_30 PM (2).png]]

These data types are different ways of storing the imputed values - 
- **wide**: MI are stored as separate columns in the dataset, so there are additional columns.  Generally used when you want to keep the original dataset structure intact and are working with smaller datasets where memory isn't an issue.
- **mlong**: Stores MI by extending the dataset, with a new row 'imputation' numbered with the specific instance of multiple imputation.  Preferred when you have a larger dataset and want to perform imputation-specific operations easily.
- **flongsep**: Imputed datasets are stored in separate files.  Ideal for very large datasets.
- **flong**: Used when there's significant variability in the variables across imputations

Long format:
![[imputation table.png]]

Wide format:
![[wide imputation table.png]]

Flongsep format:
![[separate file imputation tables.png]]

Flong format:
![[imputation table 2.png]]

- Impute categorical variables, not their dummy variables (1s not the 0s).  Also only impute base variables, not interactions.  Auxiliary variables need to be registered with the software as well.
- Generate passive variables (dummies, interactions) (also known as non-imputed variables)
- Throughout this process, explore the missing pattern and ensure the MI makes sense.  Inspecting correlations, MSD after imputation is still good even though it's synthetic.
- Doing postestimation with imputed datasets is better, because they are more descriptive of what data we're actually using in our model.  Don't need to do them for every dataset, but one or two.

Reporting missing data analyses
https://youtu.be/hgaqIqmqHjo?si=8JbvmttQZzJCSWJy