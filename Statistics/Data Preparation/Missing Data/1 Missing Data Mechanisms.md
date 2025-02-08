https://www.youtube.com/watch?v=TxcLeMsZ7Bk
[Mikko Rönkkö](https://www.youtube.com/@mronkko)

Missing data has patterns (what data are missing) and mechanisms (why data are missing).  This focuses down on mechanisms.  It's more important than the patterns, and is something we cannot fully test.  We can see patterns from the data, but need theoretical understanding to find mechanisms.

The mechanisms are not descriptively labeled:
![[Captures 8_30_2024 3_48_50 PM.png]]

MCAR - The missingness does not depend on anything in the data.  The only consequence is that there is less data for us to analyze.
MAR - Missingness that can depend on some other variables in the data, but not the value that is missing.
MNAR - The worst case.  Missingness depends on the missing values themselves.

![[Missing data mechanisms - YouTube - Google Chrome 8_30_2024 3_51_15 PM.png]]

We can immediately rule out MCAR, because the straight line of missingness shows a correlation with IQ.  We need to think: does the job performance that is unobserved, determine the missingness?  We cannot do that in an empirical dataset, because we don't have the actual job performances of the missing data.  Here, even though job performance clearly correlates, it's explained by the fact that the missing job performances depend on the IQ, and the IQ caused the missingness.  This is MAR.

MCAR - loss of efficiency, but no bias.
MAR - bias, but modern techniques can compensate.
MNAR - bias, some control with selection models.

How do we test?  
- MNAR cannot be easily tested, because we don't observe the missing value.  
- MAR vs MCAR can be tested with a t-test with multiple comparisons corrections.  Split the data into two, cases with missing and without, compare the means.  This requires a large number of tests if there are many patterns of missing variables.  
- Little's MCAR test - gives one p-value that tests for MAR vs. MCAR