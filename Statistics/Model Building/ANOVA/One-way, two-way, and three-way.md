![[langsrud2003.pdf]]

ANOVA models can evaluate the impact of one or multiple factors on an outcome variable. Choosing between \_-way ANOVAs depends on the number of factors and whether you're considering their interaction.

- ANOVA tests each factor's SS against the residual SS individually, so there is no sequential operations happening here.
- For the three-way interaction term specifically, a significant result just says that _at least one of the two-way interactions changes across the levels of the remaining variable_.
- The thing that differs when adding more factors is that the residual variance changes, so if you get better signal-to-noise by including more variables, you may get larger F-values (ratio of SS to SSE) which may initially seem confusing.
#### One-Way ANOVA
- **Definition**: Tests the effect of a **single factor** on a continuous outcome variable.
- **Purpose**: Determine if there is a statistically significant difference in the means of the outcome variable across the levels of this single factor.
- **Model Structure**: 
  - $\text{Outcome} = \mu + \tau_i + \epsilon$
  - Where $\tau_i$ represents the effect of the $i$-th level of the single factor.
- **Hypothesis Testing**:
  - **Null Hypothesis ($H_0$)**: All group means are equal (no effect of the factor).
  - **Alternative Hypothesis ($H_A$)**: At least one group mean is different.
- **Key Insight**: The F-statistic only tests the main effect of the single factor. A significant F-statistic indicates a difference in means between the groups defined by this factor.

#### Two-Way ANOVA
- **Definition**: Tests the effect of **two factors** on a continuous outcome variable, as well as the **interaction** between them.
- **Purpose**: Assess if the effect of one factor changes depending on the level of the other factor (interaction effect) and determine the main effects of each factor.
- **Model Structure**:
  - $\text{Outcome} = \mu + \alpha_i + \beta_j + (\alpha\beta)_{ij} + \epsilon$
  - Where $\alpha_i$ represents the effect of the $i$-th level of factor 1, $\beta_j$ represents the effect of the $j$-th level of factor 2, and $(\alpha\beta)_{ij}$ represents the interaction effect between factor 1 and factor 2.
- **Hypothesis Testing**:
  - **Main Effects**: 
    - $H_0$: The means are equal across the levels of each factor individually.
  - **Interaction Effect**: 
    - $H_0$: The effect of one factor does not depend on the level of the other factor (no interaction).
- **Key Insight**: A significant F-statistic for the interaction term indicates that the impact of one factor on the outcome depends on the level of the other factor.

#### Three-way
https://stats.oarc.ucla.edu/spss/faq/how-can-i-explain-a-three-way-interaction-in-anova-2/

#### Practical Differences in R
- **One-Way ANOVA Model**: 
    ```r
    aov_oneway <- aov(outcome ~ factor1, data = df)
    summary(aov_oneway)
    ```
- **Two-Way ANOVA Model with Interaction**: 
    ```r
    aov_twoway <- aov(outcome ~ factor1 * factor2, data = df)
    summary(aov_twoway)
    ```

**Graphing the interaction plot**:
```r
golfball.means <- golfball %>% 
  group_by(golfer, brand) %>% 
  summarise(mean_distance = mean(distance), .groups="drop")

ggplot(data=golfball.means, aes(x=golfer, y=mean_distance, color=brand)) + 
  geom_line(aes(group=brand)) + 
  geom_point() + 
  theme_minimal()

ggplot(data=golfball.means, aes(x=brand, y=mean_distance, color=golfer)) + 
  geom_line(aes(group=golfer)) + 
  geom_point() + 
  theme_minimal()
```
![[ANOVA-interactionplot1.png]]
![[ANOVA-interactionplot2.png]]

#### Visual Interpretation
- **Main Effect**: Parallel lines in an interaction plot suggest no interaction, while different line slopes suggest a main effect.
- **Interaction Effect**: Non-parallel lines indicate an interaction between the factors, suggesting that the effect of one factor varies depending on the level of the other.

![[meat packing example data.png]]

**Example**: Suppose we have a dataset on golf ball `distance` affected by two factors: `brand` and `golfer`. A one-way ANOVA might test if different brands yield different distances, while a two-way ANOVA would assess both the effect of brand and golfer, as well as the interaction between brand and golfer on distance.

#### Highlighting changes in F-values as you add more factors
While the SS of the factors will remain the same, adding more factors will change the residual variance.  Here we see the F-value of `heat` change as we add `machine`, because the $\text{MSR}$ changes from 9.98 to 5.98.  This is because the signal-to-noise ratio for remaining factors improves.
```r
aov1 <- aov(y ~ heat, data=steel)
aov2 <- aov(y ~ heat + machine, data=steel)

print(summary(aov1))
print(summary(aov2))
```
![[ANOVA-FvalueChange.png]]
