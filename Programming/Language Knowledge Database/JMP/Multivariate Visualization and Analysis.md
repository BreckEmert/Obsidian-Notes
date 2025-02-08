- Analyze -> Multivariate Methods -> Multivariate
	- Correlations and Correlation Matrix
	- Covariance Matrix
	- Outlier Analysis
	- Scatterplot Matrix

To create a graph matrix which is more customizable than the one in Multivariate:
![[jmp-manual-scatterplot-matrix-final.png]]
In Graph Builder, click on the 'Dialog' button, which is better in this situation.  Drag in the variables to the Y and the X, disable the smoother, and enable 'Graph matrix' in the bottom left.
![[jmp-manual-scatterplot-matrix.png]]

#### Bar charts
Add a categorical variable to a bar chart by dragging it just inside the y-axis:
![[jmp-categorical-barchart.png]]
Control the bar style as well.  Here, because city mileage is always lower than highway mileage, it is a good nested bar example:
![[jmp-nested-bar.png]]
Right click the x-axis to order by a variable.  You can also make an ordering variable, and drop it onto the x-axis title.  Here, we order by the *largest difference*:
![[Pasted image 20240913012837.png]]


Identify an interaction effect by stratifying using Overlay (top right).
![[jmp-stratified-interaction-effect.png]]
You can right click the grouping to change the number of levels of grouping:
![[jmp-group-levels.png]]
This more clearly identifies the difference in correlation between low weight cars and high weight cars:
![[jmp-clear-interaction-effect.png]]
To model this effect formally, use **Fit Model** and, before adding the variables, select both and enable **Full Factorial**.  This is the same thing as creating another variable but is more scalable.
![[jmp-full-factorial-interaction.png]]
To visualize the formal effect clearly, enable the **Prediction Profiler** where you can drag around the variables (also visible in the **Surface Profiler** and **Response Plane**):
![[jmp-prediction-profiler-dragaround.png]]
The specific contours of the can be visualized by adjusting **Contour** in the **Contour Profiler**:
![[Pasted image 20240913020300.png]]
Enable the **Contour Grid** to see many contours at once:
![[jmp-contour-grid.png]]
![[jmp-contour-profile-grid.png]]
