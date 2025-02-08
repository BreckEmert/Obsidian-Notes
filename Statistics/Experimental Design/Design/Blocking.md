read this:
https://opentext.wsu.edu/carriecuttler/chapter/experimental-design/
- covers carryover effects

- Contrasts are only estimable if the treatments share at least one block.



## Complete Block Designs
$k = sg$, where $s \in Z^+$
- $b$: Number of blocks
- $k$: Block size
- $g$: Number of treatment levels
- $r$: Number of times each treatment is observed in study (equireplicate)
- $n_{hi}$: Indicator of whether treatment $i$ is observed in block $h$
- $\lambda_{i,p}$: Number of blocks containing both treatment i and treatment $p$
#### Randomized CBD
#### General CBD


## Incomplete Block Designs
Distinguished by $k < g$.


![[Disconnected-Connected-IBDs.png]]

#### Balanced IBD
#### Group divisible design
#### Cyclic design


#### Row-column designs
- Extend the idea of blocking by introducing two independent blocking factors.
- Hence, we control for two sources of variability.  
- In most row-column designs, all treatment contrasts are estimable.
- $c$ represents the number of the second blocking factor.
- Be sure to randomly assign the row-block labels, column-block labels, and treatment labels.
Latin squares and Youden designs
![[Row-Column-designs.png]]

Crappy discussion board post I made:
[https://doi.org/10.1002/ps.4773](https://sci-hub.se/https://doi.org/10.1002/ps.4773)  
This paper was focused on one example for RCBD and an example for unbalanced design.  In the first for the RCBD they measured the mean heights of corn using the same treatment but with 10 modifying chemicals to it.  The second was .  They highlight that designs can focus on the treatments (testing different concentrations, pesticides, modifying chemicals) or on the errors (blocking, replication).  They say they notice a lot of researchers ignore blocking effects as researchers apply t-tests, ANOVA and LMM which yield the same treatment means but all mess up the standard errors in different ways.

- two sample independent t-test ignores blocking structure; each block is set up to be different from each other.  So it only accounts for within-block variability and ignores between-block.
- ANOVA takes this further and assumes everything can all be put together.  We get way tighter intervals and higher false positive rates.
- LMM still fails when blocks are omitted or incorrectly specified.  But it's the preferred method.

They cover ideas like "why not just average all the data and do statistics on that" which actually sounds like a good idea.

The last of the paper is this:

In conclusion, for experiments in which treatments are randomly assigned to plots within blocks and  
observations are taken on individual units sampled from the plots, always include random block and plot  
effects in the analysis. This is most easily done using a linear mixed model approach that contains fixed effects  
for all treatments of interest and a set of random effects for block and plots within blocks.   In special balanced cases, a more simple  
analysis based on the sample means for each plot, but still including a random block effect, will result in the  
same results as the analysis based on the linear mixed effects model including both plot and block as random  
effects. For unbalanced data, this strategy will not suffice.