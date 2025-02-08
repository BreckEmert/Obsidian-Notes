Inference in ANOVA models allows us to make informed decisions by testing hypotheses about group means. However, improper inference can lead to misleading conclusions.

- **Hypothesis Testing**: 
  - **Null Hypothesis**: The mean response is the same across all groups (no treatment effect).
  - **Alternative Hypothesis**: At least two groups have different mean responses.
- **ANOVA Model Parameterization**:
  - Two ways to parameterize the model:
    1. **ANOVA Effects Model**: Tests if treatment effects are the same across groups.
    2. **Group Mean Model**: Tests if group means are equal to the overall population mean.
- **Reduced vs. Full ANOVA Models**:
  - **Reduced Model**: Assumes no treatment effect.
  - **Full Model**: Incorporates treatment effects.

- **SST (Sum of Squares for Treatment)**: Variation due to differences between group means.
- **SSE (Sum of Squares for Error)**: Variation within groups.
- **MST (Mean Square for Treatment)**: $\text{SST} / \text{df}_{\text{treatment}}$
- **MSE (Mean Square for Error)**: $\text{SSE} / \text{df}_{\text{error}}$
- **F-Statistic**: The ratio $\text{MST} / \text{MSE}$ follows an F-distribution.

Calculating some critical statistics for this dataset:
![[ANOVA-detergentExample.png]]
($M$ = machine and $D$ = detergent)
**$\text{df}_M =  4-1 = 3$**
**$\text{df}_D = 3 - 1 = 2$**
**$\text{df}_{M*D} = \text{df}_M * \text{df}_D = 3*2 = 6$**
**$\text{df}_{total} = 48-1 = 47$**
**$\text{df}_\text{error} = 47-3-2-6 = 36$**
**$\text{SSM} = 3*4*200 = 2400$**
**$\text{SSD} = 4*4*450 = 7200$**
**$\text{SST} = \text{SSM} + \text{SSD} + \text{SS}_\text{M*D} + \text{SS}_\text{error} = 2400+7200+1800+248 = 11648$**
**$\text{MSM} = \frac{\text{SSM}}{\text{df}_M} = \frac{2400}{3} = 800$** 
**$\text{MSD} = \frac{\text{SSD}}{\text{df}_D} = \frac{7200}{2} = 3600$** 
**$\text{MS}_{M*D} = \frac{\text{SS}_{M*D}}{\text{df}_{M*D}} = \frac{1800}{6} = 300$** 
**$\text{MSE} = \frac{\text{SS}_\text{error}}{\text{df}_\text{error}} = \frac{248}{36} \approx 6.89$**


### An Example

![[meat packing example data.png]]

- **Example**: Comparing types of meat packaging on bacteria accumulation.
  - **Response Variable**: Log count of psychotropic bacteria per cm².
  - **Treatment Levels**: Different types of packaging. `length(unique(Treatment))`
  - **Hypothesis**: Does packaging type affect bacteria levels after storage?

![[one-way anova inference questions.png]]

![[Pasted image 20240831180724.png]]

So, the reduced ANOVA model is one which just has error centered around a mean.  If this model can reasonably produce the data, then we do not need the full model to explain it.
### Visual Interpretation
- **When $R$ is Large**: Suggests a significant treatment effect, evidence against the null hypothesis.
- **When $R$ is Close to Zero**: Suggests no significant treatment effect, evidence for the null hypothesis (fail to reject).

### Statistical Test
- **Decision Rule**: Reject $H_0$ if $\text{F-statistic} > F_{\alpha, df_1, df_2}$.
  - This can be determined using statistical software or an F-distribution table.

## [[One-way, two-way, and three-way]] ANOVAS
Covered in a separate note


## ANOVA power
$\text{Power} = 1 - \beta$
Where $\beta$ is the probability of a **Type II** error (failing to reject the null when it's false)
Power is the probability of correctly rejecting the null when the alternative is true (i.e., finding a significant effect when there is one).

**Interaction power**
A test for interactions has less power than a test for a main effect.  The **probability of detecting a true interaction** effect is lower than the probability of detecting a true main effect, given the same sample size and significance level.  So when we refer to interactions having less power, we're referring to **each individual interaction test having less power** to detect a true effect.  However, the overall probability of getting a positive result is higher when making more comparisons.