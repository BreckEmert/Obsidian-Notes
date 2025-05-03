
#flashcards/ML #flashcards/Stats

Gram Matrix
?
XXT
Each element $G_ij$ is the inner product of the $i_{th}$ and $j_{th}$ rows of $X$.  Assuming each row is a sample, then $1/n$ (where $n = \text{len}(G)$) times $G$ is the covariance matrix (only when the rows of $X$ are mean-centered).  When $X$ is the basis for a subspace and has orthonormal columns, it represents the projection matrix onto that subspace.  
Diagonal = square of length, off-diagonals = similarity.

Match the model complexity to the \_, not the \_.
?
data resources, target complexity
![[Simple vs Complex model e_in e_out.png]]

Curse of Dimensionality & Addressing it
?
1. Data sparsity - data is harder to fill the space
2. Distances average out - Euclidian distances become less meaningful, nearest and farthest neighbors become relatively closer.
3. Computational Complexity
4. Overfitting & Noise
Addressing:
1. Manual feature selection, esp. limiting covariance
2. Dimensionality reduction: PCA, autoencoders, tSNE & UMAP
3. Regularization
4. Feature Engineering

L2 Regularization Formula
??
$$L_{\text{reg}} = L + \lambda/2 \sum_{l} \sum_{ij}(W_{ij}^{(1)})^2$$
where:
	$L$ is the original loss function
	$\lambda$ is the regularization parameter, controlling strength
	$W_{ij}^{(1)}$ weight connecting the $i^{th}$ neuron in layer $l-1$ to the $j^{th}$ neuron in layer $l$
and:
	$\lambda/2 \sum_{ij}(W_{ij}^{(1)})^2$ adds a penalty proportional to the sum of the squared weights to avoid overfitting.
.
Weight update: $$W_{\text{new}}^{(l)} = W^{(l)}-\eta (\frac{\partial L}{\partial W^{(l)}} + \lambda W^{(l)})$$  
	$W^{(l)}$ weights of the $l^{th}$ layer before the update $\eta$ learning rate
	$\frac{\partial L}{\partial W^l}$ Gradient of loss wrt weights
	$\lambda W^{(l)}$ regularization term that penalizes large weights

LASSO behavior when there is multicollinearity::Lasso cannot decipher colinear variables, it can only offer a penalty of $+$ or $-$ $\lambda$.  It selects a variable and pull the others to 0, which can be more random than Ridge.  That is, it just picks whatever predictor does better in the train set, and does not take a careful approach.

Bias (in the context of bias/variance)
??
The difference between the average prediction & datapoints.  It's the error introduced by approximating a real-world problem with a simple model, a model not complex enough to describe the full relationship present in the training data.  Given a true $f(x)$ (which we cannot really know):
$$\mathbb{E}[\hat{f}(x)] - f(x)$$

Variance (in the context of bias/variance)
??
The average squared distance (MSE doesn't *have* to be used) between predictions and the mean prediction.  The error introduced by using a model too complex for the true underlying patterns in the data.
$$\mathbb{E}[\, \hat{f}(x) - \mathbb{E}[\hat{f}(x)]^2 \,]$$
<!--SR:!2025-04-03,2,248!2000-01-01,1,250-->

Negative Predictive Value (NPV)
Formula & Description
??
$$\text{TN} / (\text{TN} + \text{FN})$$
Only about the negative predictions.  Concerns the cost of actions we don't take.  What % of our actions on the real predicted negative class are real negatives?  Given a negative prediction, NPV is the probability that it is correct.

Sensitivity & Specificity are how we understand the \_.
Precision and NPV are how we understand our \_.
?
original data
model

Precision Formula & Description
also known as \_ \_ \_.
??
$$\text{TP} / (\text{TP} + \text{FP})$$
Only about the positive predictions.  Concerns the efficacy of the actions we take.  What % of our actions on the predicted positive class are on real positives?
Also known as PPV.
<!--SR:!2000-01-01,1,250!2025-04-02,1,228-->

Sensitivity Formula & Description
Also known as \_\_\_\_\_\_\_ and \_ \_ \_.
??
$$\text{TP} / (\text{TP} + \text{FN})$$
Only about the true positive class.  What % of the positive class is attended to by our actions?
Also known as Recall, TPR.

Specificity Formula & Description
Also known as \_ \_ \_.
??
$$\text{TN} / (\text{TN} + \text{FP})$$
Only about the negative class.  What % of the negative class is attended to by our actions?
Also known as TNR.
<!--SR:!2000-01-01,1,250!2025-04-02,1,228-->

Reminder about Sens, Spec, Prec, NPV
?
**Sensitivity (TPR) and Specificity (TNR)** are intrinsic to the test and are independent of the class distribution (prevalence) - they describe how well the test detects the actual condition in the data.
**Precision (PPV) and NPV** depend on the decision threshold and the underlying class distribution, reflecting how reliable the model's positive and negative predictions are.
<!--SR:!2025-04-02,1,230-->

What does the graph of precision, recall and F1 score look like?
?
The formula is $F1 = \frac{2\times\text{precision}\times\text{recall}}{\text{precision}+\text{recall}}$.  Since it's normalized it peaks at 1, and parabolically goes to 0.  For balanced cases of $\text{precision} = \text{recall}$ it goes linearly to 1.
![[f1_score_graph.png]]