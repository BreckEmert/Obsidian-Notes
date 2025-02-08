https://www.youtube.com/watch?v=OdtLqspWwxo

## Centering
The only thing that differs with centering is the intercept of the model.  The coefficients, **including the interaction coefficient**, will remain the same.
![[Centering_Model_Comparison.png]]

The actual difference comes from the fact that in the uncentered model **$x1$ and $s2$ are effects at $x1x2 = 0$**, while the centered model shows the effects at the **mean value of $x1x2$**.  In this plot we see the (uncentered) effect of $x1$ when $x2 = 0$ in the blue line.  When we center, the effect of $x1$ becomes the green line.  The centering just influences which part of the regression plane we are looking at.
![[InteractionEffect_3dGraph.png]]
We can't summarize the effect of $x1$ by just looking at one line.  In either case, we need the entire regression plane to summarize the effect.  That is, the effect of $x1$ depends on the value of $x2$.  Since we have to show several/other plots anyway, centering is pointless.  It's just a way of changing which line we show when we need to show multiple of them anyway.

The downside of centering is that once we calculate predictions, they will be off by the centered amount.  Doing plots that apply predictions and getting estimated values is more difficult.

