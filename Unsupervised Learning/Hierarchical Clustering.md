**Hierarchical Agglomerative Cluster Analysis (HAC)**: This is the primary method used for hierarchical clustering because it lets you use various linkage methods under the same template.  It updates the proximities between the emergent (merged of two) cluster and all the other clusters (including singleton objects) existing so far.  It does this using the **Lance-Williams** formula.

There are three parameters:
- **alpha**: 
- **beta**: 
- **gamma**: 

There are many possible methods:
- **Single linkage** or **nearest neighbor**:  Proximity between two clusters is defined as the proximity of their two closest objects
- **Complete linkage** or **farthest neighbor**:
- **Between-group average (UPGMA)**: average of the nearest and farthest neighbor
- **Simple average**: average of all of the objects
- **Centroid (UPGMC)**: geometric centroids using squared Euclidian distance.  
- **Median** or **equilibrious centroid (WPGMC)**: Modified version of **centroid**
- **Ward's** or **minimal increase of SS (MISSQ)**: The magnitude by which the SS in their joint cluster will be greater than the combined SS in the two clusters.  Very similar to k-means, as it shares the same objective function of minimizing the pooled within-cluster SS.  While k-means is usually a better minimizer of this function, Ward may be more accurate at uncovering clusters of uneven variances or clusters which are spaced inconsistently.

## Choosing a method
Say it with me!
- **Prior assumptions**: 
	- ***"I prefer this method because it constitutes clusters in a way that matches my concept of a cluster in my particular object."***
	- Each clustering algorithm is biased to select different shapes and types of clusters; they have implied structure/build/shapes of their clusters.
- **Data/method assumptions**: 
	- ***"I preferred this method because the nature of my data or its format predisposes it."***
	- Different methods require different kinds of data.  Quantitative and qualitative data have different methods, especially in mixture data.  Ward's/k-means are based on Euclidian distances and not on an arbitrary measure.  Binary data might call for special similarity measures which would be highly suspect with certain methods.  Big data may need special algorithms or implementations.
- **Internal validity**: 
	- ***"I prefer this method because it gives me the most clear-cut, tight, and isolated clusters."***
	- This claims that the information contained by a set of objects was utilized by the partitioning procedure.  Sometimes you just choose the method which yields the best results.  Especially relevant of a priority for some (early) exploratory analyses.
	- [[#Internal indices]] measure performance without external information.
- **External validity**:
	- ***"I prefer this method because it gives me clusters which differ by their background or which match the ones I know."***
	- [[#External indices]] are usually preferred, evaluating the results of the algorithm based on a known cluster structure or cluster labels.
- **Cross-validity**:
- ***"I prefer this method because it gives me similar clusters on equivalent samples or extrapolates well."***
- Two main approaches are to check for:
	- **Stability**: Randomly split or resample the data in partly intersecting or fully disjoint sets and cluster each, then match and compare the solutions wrt some emergent cluster characteristic (e.g., central tendency location), seeing if it is stable across the sets.
	- **Generalizability**: Cluster on a train set and use its emergent cluster characteristic/rule to assign objects of a test set, and also cluster the test set.  The assignment and cluster result's cluster memberships of the test set objects can then be compared.
- **Interpretation**:
	- ***"I prefer this method because I believe its clusters to be meaningful and interpretable."***
- **Lastly**, you may achieve similar results with a variety of methods, and just decide one.  This isn't optimal, but can happen often.  You may not be able to significantly justify a particular method.

### Internal indices
- https://stats.stackexchange.com/questions/21807/evaluation-measures-of-goodness-or-validity-of-clustering-without-having-truth
- Silhouette index (implementation in [MATLAB](http://www.mathworks.com/help/stats/silhouette.html))
- Davies-Bouldin
- Calinski-Harabasz
- Dunn index (implementation in [MATLAB](http://www.mathworks.com/matlabcentral/fileexchange/27859-dunns-index))
- R-squared index
- Hubert-Levin (C-index)
- Krzanowski-Lai index
- Hartigan index
- Root-mean-square standard deviation (RMSSTD) index
- Semi-partial R-squared (SPR) index
- Distance between two clusters (CD) index
- weighted inter-intra index
- Homogeneity index
- Separation index

### External indices
- Rand
- F-measure
- https://stats.stackexchange.com/questions/586470/measures-to-compare-clustering-partitions

