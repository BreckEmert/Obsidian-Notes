#### Overview
BRB is a knowledge-driven model that, because of its utilization of domain knowledge in establishing its rule-base, requires a smaller dataset for training.  It's sensitive to its referential values but offers a powerful and interpretable way to make decisions.

Each rule in BRB follows an "if-then" structure:$$
\begin{align}
R_k: & \text{If } X \text{ is } A^k \text{, then } (D_1, \beta_{1,k}), \dots, (D_n, \beta_{N,k}) \\
& \text{with rule weight } \theta_k \text{ and attribute weight } \delta_k
\end{align}
$$- where:
	- $R_k$ denotes the $k\text{th}$ rule
	- $X$ is the preconditions of the input sample
	- $A^k = \{A_1^k, A_2^k, \dots, A_M^k\}$ represents the set of referential values in $R_k, k \in \{1, 2, \dots, L\}$
	- $M$ and $L$ are the number of the antecedent attributes and rules, respectively
	- $\beta_{i,k}$ is the belief degree of the $i\text{th}$ result $D_i$ according to $k\text{th}$ rule
	- $N$ is the total number of results

From this equation we can see that by assigning the semantic information to each referential value of each attribute, the semantic information of each rule can be interpreted well, which is the critical property of the BRB model.
#### Terminology Dictionary
**Outcome**
- Outcomes could be categorical labels (e.g., "high risk", "medium risk", "low risk") or numerical values (e.g., "system health = 0.8").
**Belief Degree $\beta_{j,k}$**
- Belief degrees quantify the level of belief (probability) that a certain consequent (outcome) is true for a given rule $k$.
- There is one for each consequent of a rule (e.g., $\beta_\text{Low Temp}$, $\beta_\text{High Temp}$)
- Belief degrees from lower-level rules only get applied when a hierarchical rule structure is employed (lower-level rules defining higher level ones).
**Rule Weight $\theta_k$**
- Quantifies the importance of the $k\text{th}$ rule relative to other rules, and affects how much influence the rule has when combining belief degrees across multiple rules.
**Knowledge Acquisition**
- The process of defining rules, belief degrees, attribute weights, and other parameters.
- Can be automated by optimization algorithms (e.g., genetic, evolutionary, gradient-based).

## RIMER
#### Overview
Traditional rule-based systems are good at defining "if-then" rules, but they fail to handle nuance in uncertainty.  For instance, if we have conflicting or partial evidence (e.g., temperature is *almost* high or the humidity data is missing), a traditional system would either ignore these cases or give a binary decision without confidence.

RIMER uses belief degrees and Dempster-Shafer Theory to explicitly model these situations.  This allows it to combine different types of uncertainty (fuzziness, ignorance, belief-based uncertainty) in a mathematically sound way, which a traditional set of rules cannot do.
#### Core bullets:
- Rule-based systems either use fuzzy logic or probability theory, but not both.  RIMER integrates these frameworks using the Evidential Reasoning (ER) approach, allowing it to represent both vague linguistic information and belief-based (*the word 'probabilistic' was intentionally not used here*) outcomes simultaneously.
	- "If temperature is slightly high" and "If the probability of system failure is 30%" can be combined into a single rule without needing two separate systems for fuzziness and probabilities.
- Rules are structure hierarchically, where the lower-level rules (e.g., sensor measurements) feed into the higher-level rules (e.g., system health assessment).  This modular structure allows for a layered understanding of the system.
	- Each antecedent and each rule has a different weight, allowing dynamic weighting based on the context or current belief about the importance of each attribute.
- NNs learn feature hierarchies through backpropagation, but RIMER builds its hierarchy through logical and belief-based transformations which preserve interpretability and transparency.
	- Disclaimer: RIMER can be structured hierarchically or flat (breadth vs depth), depending on the (complexity of the) problem.
	- RIMER adjusts rule parameters (belief degrees, attribute weights, and rule weights) through knowledge acquisition or optimization techniques like manual tuning, and in some cases, learning-based approaches like genetic or evolutionary algorithms.
	- The ER approach modifies Dempster's combination rule with reliability factors ranging from 0 to 1, assigned to each piece of evidence for use during belief aggregation.  Higher scores reflect greater confidence and ensure unreliable sources don't dominate a result.

## Optimization Models
#### Overview
Manually setting parameters may not consistently minimize MSE due to fixed weights and predefined belief distributions. While traditional BRB systems can incorporate optimization, they may still face challenges in fitting complex numerical datasets and can be sensitive to initial parameter settings.
#### Single-Objective Optimization
- **Objective**:  
  - Minimize the mean squared error (MSE) between observed and predicted outputs for numerical data:
	  - $\min_{\beta, \theta, \delta} \frac{1}{N} \sum_{i=1}^{N} \left( \hat{y}_i - y_i \right)^2$
	  - where:
		  - $\hat{y}_i$ is the predicted output from the BRB system.
		  - $y_i$ is the observed value for the $i$-th sample.
		  - $N$ is the number of training samples.
- **Mathematical Constraints**:
	- $\beta_{j,k} \in [0, 1]$ - (belief degree constraint)
		- Represents the degree to which $D_k (\in D)$, the consequent in the $k\text{th}$ rule, is believed to be the consequent.
	- $\theta_k \in [0, 1]$ - (rule weight constraint)
		- Represents the relative importance of the rule to the associated conclusions.
		- Attribute and Rule weights can be assigned at the knowledge acquisition phase when a rule base is established.  Scaling methods, pairwise comparisons, geometric least squares or geometric mean can be used to estimate the relative weights.
	- $\sum_{i=1}^{I_k} \delta_{i,k} = 1$ - (attribute weight normalization)
		- For each rule $k$, the attribute weights $\delta_{i,k}$ are normalized such that $\sum_{i=1}^{I_k} \delta_{i,k} = 1$, where $I_k$ is the number of antecedents in rule $k$.
		- Attribute weights are the relative importance of an attribute to its consequent.
- **Benefits**:
	- Uses MSE to guide parameter adjustments while traditional BRB uses subjective tuning.
	- The sum constraint on $\beta$ allows optimization even with missing or uncertain data, dynamically redistributing belief degrees.
- **Drawbacks**:
	- While single-objective optimization fits quantitative data, it cannot balance trade-offs between conflicting goals or handle subjective judgments. 
	    - In a health evaluation system, a single MSE objective cannot balance the trade-off between minimizing FN (undetected disease) and FP (unnecessary treatments).
- **Example**:
	- For predicting equipment failure probabilities, the system initially assigns belief degrees based on predefined rules like “If temperature is high, failure probability is 70%.” The single-objective optimization refines $\beta$ values and $\theta_k$ weights to minimize the error between observed equipment failures and predicted probabilities, ensuring more accurate reliability predictions.
#### Multiple-Objective Optimization
- **Objective**:
	- One method to handle multi-objective optimization in RIMER is the **minimax** approach, which minimizes the maximum residual error among all objectives. Other methods, such as weighted sum or Pareto front optimization, can also be applied depending on the specific requirements of the problem.
	- The **minimax** approach transforms the multi-objective problem into a single-objective problem by minimizing the maximum residual error among all conflicting objectives:
	    - $\min_P \max_j \left\{ \omega_j \frac{\xi_j(P) - \xi^*_j}{\xi^+_j - \xi^*_j} \right\}, \quad \text{for } j = 1, \ldots, N,$
	    - where:
		    - $\xi_j(P)$ is the residual error for the $j$-th objective function.
		    - $\xi^*_j$ and $\xi^+_j$ are the minimum and maximum feasible residuals for the $j$-th objective.
		    - $\omega_j$ is the weight assigned to the $j$-th objective for relative importance.
	- Ensures the solution respects predefined bounds and constraints while minimizing the worst-case scenario for all objectives.
- **Mathematical Constraints**:  
    - Each belief degree $\beta_{j,k}$ is constrained to the range $[0, 1]$ to ensure it represents a valid probability distribution, with the sum of belief degrees in each rule $\sum_{j=1}^{N} \beta_{j,k} \leq 1$ to handle incomplete information.
    - Rule weights $\theta_k$ are normalized to $[0, 1]$ to reflect their relative importance, ensuring no rule disproportionately dominates the final inference.
    - Attribute weights $\delta_i$ are similarly constrained, and are normalized across all attributes in a rule to maintain consistency.
  - **Benefits**:
    - By minimizing the maximum error, the minimax approach ensures that no single objective is disproportionately sacrificed.
    - Incorporates numerical *and* subjective objectives.
    - Predefined constraints adjust belief degrees even with missing or incomplete input data.
  - **Drawbacks**:
    - The minimax formulation increases computational overhead, especially for systems with many objectives and constraints.
    - Determining appropriate weights $\omega_j$ for each objective requires careful consideration, as improper weighting can bias the solution and reduce effectiveness.
  - **Example**:
    - In a hierarchical BRB system for assessing system safety, where the objectives include minimizing both false positives (i.e., incorrectly predicting system failure) and false negatives (i.e., failing to detect actual system failure), the minimax formulation can balance these competing goals, preventing the model from favoring one over the other. The constraints ensure that belief degrees for each rule are updated consistently, maintaining interpretability and reliability of the final decision.

## Evidential Reasoning (ER)
- **Overview**:
	- The rules are defined by the researcher but ER combines them to get the final decision. It weighs the belief degrees by confidence in their usefulness/reliability, then combines them while ensuring that if two rules disagree strongly, neither dominates the final result.
- **Specific Method**:
	- ER combines belief degrees ($\beta$) into a probability vector using weighted aggregation: $$\beta_{\text{combined}} = \frac{\sum_{i=1}^{N} \omega_i \beta_i}{\sum_{i=1}^{N} \omega_i}
    $$
		- Where:
			- $\omega_i$ is the reliability factor of evidence $i$.
			- $\beta_i$ is the belief degree for rule $i$.
	- Then uses Dempster’s combination rule to combine beliefs from multiple sources:$$
    m(A) = \sum_{B \cap C = A} \frac{m_1(B) \cdot m_2(C)}{1 - \sum_{B \cap C = \emptyset} m_1(B) \cdot m_2(C)}
    $$
		- Where:
			- $m(A)$ is the combined belief for outcome $A$.
			- $m_1(B)$ and $m_2(C)$ are beliefs from two independent sources.
		- Reduces overconfidence when disagreement exists.
- **Benefits**:
	- Handles multiple forms of uncertainty (e.g., ignorance, fuzziness).
	- Reduces overconfidence by adjusting beliefs through combination rules.
- **Drawbacks**:
	- Computationally intensive when combining large numbers of rules.
	- Has unstable inference when initial belief degrees aren't calibrated.

#### Dempster's rule breakdown (currently written by AI)
1. **Belief Mass Functions and How They Interact:**
	- **Belief Mass Functions ($m_1$ and $m_2$)**:
		- A belief mass function $m$ represents the probability mass assigned to different subsets of the set of all possible outcomes.
		- For example, if the possible outcomes are $A = \{\text{Low Temperature}, \text{Medium Temperature}, \text{High Temperature}\}$, then:
			- $m_1(\text{Low Temperature}) = 0.4$ might indicate that source 1 assigns a 40% belief mass to "Low Temperature."
			- $m_1(A) = 0.1$ could indicate a 10% belief in **any** outcome happening (i.e., uncertainty or ignorance).
	- **Combination of Two Belief Mass Functions**:
		- Dempster’s rule combines $m_1$ and $m_2$ to form a new mass function $m(A)$. This combined function reflects the degree of agreement between the two sources.
2. **Formula Breakdown:**
   The rule for combining two belief mass functions $m_1$ and $m_2$ is:  $$
   m(A) = \sum_{B \cap C = A} \frac{m_1(B) \cdot m_2(C)}{1 - \sum_{B \cap C = \emptyset} m_1(B) \cdot m_2(C)}
   $$
	- **Numerator**:  $$ \sum_{B \cap C = A} m_1(B) \cdot m_2(C) $$
	     - This term aggregates the combined belief mass for all pairs of sets $B$ and $C$ such that their intersection is exactly $A$.
	     - It essentially **multiplies the beliefs** $m_1(B)$ and $m_2(C)$ whenever the intersection $B \cap C = A$.
	     - Intuition: If both $B$ and $C$ overlap exactly on $A$, then this indicates **agreement** on $A$, so their joint belief is strengthened.
	- **Denominator**:$$ 1 - \sum_{B \cap C = \emptyset} m_1(B) \cdot m_2(C) $$
	     - This term **normalizes** the result by subtracting the total conflict (i.e., cases where $B \cap C = \emptyset$, meaning $B$ and $C$ have no common elements).
	     - It measures the degree of conflict between the two sources. The more they disagree (i.e., $m_1(B) \cdot m_2(C)$ for $B \cap C = \emptyset$ is high), the more the final belief mass is scaled down.
	     - Intuition: If the sources have significant conflict, we reduce their combined influence, preventing overconfidence in any outcome.
1. **Conflict Handling:**
	- **If $B$ and $C$ Disagree Strongly**:
		- When $B \cap C = \emptyset$, meaning that $B$ and $C$ are mutually exclusive, $m_1(B) \cdot m_2(C)$ contributes to the conflict term in the denominator.
		- This increases the denominator, which **decreases** the combined belief mass $m(A)$ for all outcomes. Essentially, it penalizes the combined belief when the two sources disagree significantly.
	- **If $B$ and $C$ Agree Strongly**:
		- When $B \cap C = A$, the numerator increases, and since $B$ and $C$ overlap exactly on $A$, the combined belief in $A$ is **strengthened**.
		- The rule rewards agreement by amplifying the combined belief mass when sources agree.
1. **Resulting Properties of the Combined Belief Mass $m(A)$**:
   - **Normalization**:  
     The denominator$$ 1 - \sum_{B \cap C = \emptyset} m_1(B) \cdot m_2(C) $$ensures that the total belief mass sums to 1 after considering all conflicts.  
     This guarantees that $m(A)$ remains a valid probability distribution.
   - **Conflict Redistribution**:  
     Conflict is redistributed among non-conflicting outcomes, ensuring that if two sources strongly disagree, their influence on any single outcome is reduced proportionally.
5. **Example to Illustrate the Formula**:
   Suppose we have two sources:
   - **Source 1**:
     - $m_1(\text{Low Temperature}) = 0.5$
     - $m_1(\text{High Temperature}) = 0.3$
     - $m_1(A) = 0.2$ (general uncertainty)
   - **Source 2**:
     - $m_2(\text{Low Temperature}) = 0.6$
     - $m_2(\text{High Temperature}) = 0.1$
     - $m_2(A) = 0.3$ (general uncertainty)
   **Applying Dempster’s Rule**:
   - **Agreement on Low Temperature**:  
     The belief mass for "Low Temperature" will be:$$
     m(\text{Low Temperature}) = \frac{0.5 \times 0.6}{1 - (0.5 \times 0.1 + 0.3 \times 0.6)} = \frac{0.3}{1 - 0.23} = \frac{0.3}{0.77} \approx 0.39
     $$
   - **Agreement on High Temperature**:
     The belief mass for "High Temperature" will be:$$
     m(\text{High Temperature}) = \frac{0.3 \times 0.1}{1 - (0.5 \times 0.1 + 0.3 \times 0.6)} = \frac{0.03}{1 - 0.23} = \frac{0.03}{0.77} \approx 0.04
     $$

   - **Conflict Contribution**:
     The conflicting beliefs (e.g., $m_1(\text{Low Temperature})$ vs. $m_2(\text{High Temperature})$) reduce the overall influence of both beliefs, ensuring that the final combined belief is not overconfident.
### Summary:
- **Numerator**: Captures agreement by multiplying beliefs that intersect on the same outcome.
- **Denominator**: Normalizes by subtracting conflicts, reducing overconfidence in the presence of disagreement.
- **Final Output**: A probability distribution that balances agreement and disagreement, ensuring consistent results.
