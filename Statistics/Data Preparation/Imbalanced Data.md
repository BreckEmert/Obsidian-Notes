Also see [[Propensity Scores]] or [[Causal Inference Methods (Propensity Score)]]

## Let's go ahead and address SMOTE
![[smote_example.jpg]]

- Be cautious of t-sne visualizations of SMOTE.  I am **extremely** against using them, as t-sne directly compensates for any weaknesses of SMOTE.  t-sne is non-linear and prefers to keep local structures.  So if you visualize your original dataset with t-sne, it will presumably have some amount of separation in the data.  If you then generate points between your existing points in the underrepresented class, the t-sne visualization will look more distinct.  Duh.
![[tSNE_SMOTE_viz.png]]
- In 2025, we're going back to basics.  Class-weighted loss functions are just better in the majority of contexts:
	- Model-agnostic and requires no extra computation, and generates no extra points which require more computation.
	- Keeps all learning on real data
- Smote can be better in small, low-dimensional datasets
- My only claim is that SMOTE is over-trusted due to visual artifacts and complexity bias.

So when can we use SMOTE?  Surely there's a comparable idea?  Surely something must be better than RUS or class-weighted loss functions?  This is my pick.  There's a lot of options, and you can only cover so many things.  The difference between all of them is extremely small as well.  But this one is nice from a computational and theoretical perspective - it does exactly what I naively thought we should do.  It is shown to have some good results.

## Simplicial SMOTE
![[simplicial_smote_example.png]]
Simplicial SMOTE attempts to move the local decision boundaries towards the major class.  In the above example we see three minority red points and the convex line in which they would be generated (**a)** is SMOTE and **b)** is Simplicial SMOTE).  You see it push that line towards the majority class point highlighted as a blue cross.
We see below how this concept helps avoid topological holes in the data.  Let's clarify what Simplicial SMOTE does.  SMOTE draws lines between points and places it somewhere on that line.  But Simplicial SMOTE actually looks for groups of points that are all near each other - not just two at a time.  When it finds a shape, it draws a shape that connects them all (a simplex).  It only places new synthetic points inside these shapes.  So it's considering a higher dimensional structure of the data.
![[SMOTE_topological_holes.png]]
