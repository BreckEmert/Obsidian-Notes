## Tables -> Missing Data Pattern
![[JMP-DataExploration-MissingData-Pattern.png]]

You can run the `Cell Plot` script that was saved to the table to visualize the pattern column.  The last row of the cell plot corresponds to the last row of the missing data pattern.
![[JMP-DataExploration-MissingData-CellPlot.png]]
This example shows that the missing values for temp are causing most of the problems.

The `Treemap` Script creates a tree map of the missing values.  When you hold your mouse over a block, it shows you the details.  This example shows that $27\% + 69\%$ of the data either has no missing data, or is just missing `Temp`.  This is good - we have a problem for missing values with `Temp`, but otherwise might not be a big problem with this dataset.
![[JMP-DataExploration-MissingData-Treemap.png]]


## Screening -> Explore Missing Values
JMP-DataExploration-MissingData-ExploreMissingValues-Cluster

Use **Missing Value Clustering** to find commonalities among rows and columns.  In this example, the highlighted row shares two missing values with the next, and only differs by one missing value.  The same works for columns, where we see the same missingness having some relationship in the columns.
![[JMP-DataExploration-MissingData-ExploreMissingValues-Cluster.png]]