## Odds Ratios and Relative Risk

![[2.8FullQuestionScreenshot.png]]
a) 
- **Odds (White)**: $\frac{0.906}{1 - 0.906}$
- **Odds (Black)**: $\frac{0.847}{1 - 0.847}$
- **Odds Ratio**: $\frac{\text{Odds (Black)}}{\text{Odds (White)}} = 0.574$ (57%)
- **Relative Risk**: $\frac{p_{\text{Black}}}{p_{\text{White}}} = 0.935$ (93%)
- **Key Takeaway**: The **odds ratio** should not be interpreted as a **relative risk**. The correct statement is that doctors are 93% as likely to order catheterization for blacks as for whites.

b) Odds ratios are the ratio of the likelihood of an event occurring to it not occurring, while probability is the likelihood of the event out of all outcomes. The actual statement should be that doctors are 93% as likely to order cardiac catheterization for blacks as whites.

## Chi-Squared Test for Independence

![[2.17TableScreenshot.png]]
a) The null hypothesis is that there is no correlation between political party and race, and the alternative hypothesis is that there is a relationship. We will reject the null hypothesis if the p-value of the chi-square test of independence is less than the alpha of 0.05. We find the associated chi-squared value to be 184.32 with an associated p-value of $p < 0.0001$, which allows us to reject the null hypothesis and conclude that there is a significant correlation between political party and race at the 0.05 significance level.

b) For Democrats and Republicans, we see values outside of a +/- 2 range in Democrats and Republicans. In addition, we see related values of lower white Democrats and more black Democrats, as well as more white Republicans and fewer black Republicans. This suggests that most of the dependence lies in these groups’ disparity.

c) **Grading Issue**: For partition analyses, we use $G^2$ instead of $X^2$. For the first partition (Dem vs. Rep), you should get $G^2 = 213.62015$, and for the second partition (Dem + Rep vs. Ind), you should get $G^2 = 0.28091792$ (-2 points).
