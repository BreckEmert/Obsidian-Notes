https://www.youtube.com/watch?v=Q-CQGBY31CA&list=PL6tc6IBlZmOVfUv7mpfoNWmBOXX0xtPJj


https://www.youtube.com/watch?v=Q-CQGBY31CA

**Mediation**
- Effects of one variable ($X$) to another ($Y$) goes through a third variable ($M$)
	- The effect of $X$ is *mediated* by $Y$.  The causal relationship goes from $X$ to $M$ and from $M$ to $Y$, e.g., studying causes learning and learning causes performance on the exam.
- Useful for studying *mechanisms*.
**Moderation**
- The strength of effect of one variable ($X$) on another variable ($Y$) depends on a third variable ($M$).
	- If $X$ is the amount of weight training you do and $Y$ is the amount of muscle mass you gain, then $M$ could be the amount of eating that you do.
- Useful for studying *context*.
![[Pasted image 20241009121920.png]]

## Moderation
**Why are moderation models interesting?**
Moderation models help us understand how the relationship between variables changes depending on the level of a third variable $m$.  Without accounting for its effect, the correlation between $x$ and $y$ can be misleading if it varies significantly across different levels of $m$.
#### Moderation with regression
We establish an initial multi-level equation:
$y = \beta_0 + \beta_1x + \beta_2m + u$
$\beta_1 = \beta_{m1} + \beta_{m2}m$

This simplifies to:
$y = \beta_0 + (\beta_1 + \beta_2m)x + \beta_3m + u$
- The effect of $x$ has some base value $\beta_1$ and then depends also on the value of $m$.
- If $\beta_2$ is large, it means $m$ has a strong *moderating* effect.
$y = \beta_0 + \beta_1x + \beta_2mx + \beta_3m + u$
- This is just an interaction model while we have a single moderator and independent variable.

If we were to say that the moderator $m$ interacts with multiple predictors, 
- $y=\beta_0+\beta_1x_1+\beta_2x_2+\beta_3m+\beta_4(x_1 \times m)+\beta_5(x_2 \times m)+\epsilon$
	- `y ~ x1*m + x2*m` in R
	- $m$ moderates the effect of both $x_1$ and $x_2$ but doesn't imply interaction between the two.
If we were to say that two moderators interact with the same $x$, 
- *independent moderators formula*
	- `y ~ x*m1 + x*m2` in R
	- $m_1$ and $m_2$ moderate the same $x$ but do not interact between themselves.
If the moderators are allowed to interact with themselves, creating a three-way interaction, 
- $y = \beta_0 + \beta_1x + \beta_2m_1 + \beta_3m_2 + \beta_4(x \times m_1) + \beta_5(x \times m_2) + \beta_6(m_1 \times m_2) + \beta_7(x \times m_1 \times m_2) + \epsilon$ 
	- `y ~ x*m1*m2` in R
	- $m_1$ and $m_2$ interact with each other and simultaneously moderate the relationship between $x$ and $y$.

To capture a u-shaped relationship, you include a squared term for $x$ and its interaction with $m$:
$y = \beta_0 + \beta_1x + \beta_2m + \beta_3(x * m) + \beta_4x^2 + \beta_5(x^2 * m) + \epsilon$

## Mediation

**Mediation with regression**
![[Pasted image 20241009123045.png]]

**Full mediation** means that after we control for the mediator, there is no effect on the $y$.
- If a person studies really hard ($x$) but doesn't learn anything ($m$), we don't expect to see any change in their exam score $y$.
**Partial mediation** means that after we control for the mediator, the effect on $y$ is reduced but not eliminated.
- If a person studies hard ($x$) but doesn't learn anything ($m$), but picks up test-taking skills, their exam score $y$ might still be improved.