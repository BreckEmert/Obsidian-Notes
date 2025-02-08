## Data Visualization

A good way to find clusters is to select points that are around the highest density regions of data.  Go to `Fit Y by X` and enable `Select Points by Density`, and to select the densest 50% of points you can set `Low Probability = 0.5`
![[JMP-Clustering-YbyX-Density.png]]
The mesh plot will show a 3d version of this which can be useful.

The `Scatterplot Matrix` in `Multivariate` is useful.  Enable `Nonpar Density`:
![[Pasted image 20240913184531.png]]Click `L` to get the lasso tool and draw around a cluster to see what you find:
![[Pasted image 20240913184704.png]]


## K-Means Clustering

![[K-means_convergence.gif]]

To check a range of clusters, use `Iterative Clustering`.
![[JMP-Clustering-IterativeClustering.png]]
Save it to a data table:
![[JMP-Clustering-SavetoDataTable.png]]
Graph CCC vs. NCluster:
![[JMP-Clustering-MultipleClustersElbow.png]]

To analyze the cluster fit *not* in PC space and instead with the original data, save the `Clusters`:
![[Pasted image 20240913211831.png]]
Scatterplot 3d:
![[jmp scatterplot3d clusters.png]]
![[Pasted image 20240913211902.png]]

