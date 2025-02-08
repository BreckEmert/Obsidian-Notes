https://www.youtube.com/watch?v=9FvZ7Pu2MVI
Mikko Rönkkö

In a research study, we cannot study full populations because of practical reasons.  So, we rely on samples.  When we take that sample there are multiple things we need to consider and a number of things that can go wrong, that can either produce biased or inefficient results.

The typical sample in a statistical book is a random sample.  Assuming that the sample is random simplifies things a lot.

![[Pasted image 20240904093641.png]]

Example:
- **Target population**: Young Finnish High Tech Firms
- **Sampling frame**: `BusinessID` 0-3 & `TOL-08` 62 or 72
	- Does our operational definition match the conceptual one?
- **Sample**: 1000 firms randomly selected
	- How large of random error do we get because of the selection?
		- When we plan for sample size we have to consider the response rate.
- **Actual data**: 10% response rate & 100 answers...
	- The people who responded may not be randomly chosen.  If, only a random portion of these 1000 companies that were invited actually participated, we only have *inefficiency*.
	- **If these are chosen systematically, *MCAR* or *MAR*, then any regression analysis using the dependent variable as the basis for sampling may produce biased results.**

Why would selection bias matter?
#### Example 1: The impact of level of education on incomes
![[Pasted image 20240904094442.png]]
![[Pasted image 20240904094511.png]]

The **halo effect** is attributing success to things done in the past, even though it may have been random chance.
![[Pasted image 20240904095501.png]]

#### Example 2: Selection Effect in College Admissions
SAT score and motivation are weakly and positively dependent on each other.  College entry depends on both of them.  You can see the weak positive correlation in the plot, around 0.1.
![[Pasted image 20240904095628.png]]

Observing only students who were admitted to the college, there is a strong negative correlation.
![[Pasted image 20240904095733.png]]

If you were the principal of the college, you'd have to ask if this replicates in the students who didn't get accepted.  And yes, it does:
![[Pasted image 20240904095828.png]]

Both of these negative correlations are related to how we sampled, because of the **selection effect** and the outcome is **selection bias**.

Whenever you take a sample, unless you are careful to make sure that the sample is a random sample of the population under study, then you risk having selection bias in your analysis.

#### Example 3: Is a container home a good idea?
![[Pasted image 20240904100107.png]]

Why might this scenario, where a poll is conducted on the balcony of the container home, produce selection bias?  Of course, people who are not interested at all just walk past.  You have to show enough interest to go into the container home.  The counter argument is that you only want people to rate the home who have seen the interior, but that's not as important as our selection effect.



## Cluster sampling

The second most common sampling method is a **cluster sample**.  It involves dividing the population into groups and then randomly selecting clusters to sample from.  All individuals within the selected clusters are sampled, so the observations are no longer equally likely to be selected.

**Example**: If you interview Finnish people at their homes, you have to travel all over Finland to get your data.
- It's impractical to travel the country randomly.  In practice we choose some cities, some streets in those cities, and interview everybody on those.  This is highly efficient.
- **Cluster effect**:
	- If your neighbor is interviewed, then it's more likely that you will be interviewed as well.  If you live close to people who are likely to be selected, you are likely to be selected as well.
	- This can reduce the overall variance but might make the sample less generalizable.

## Stratified sampling

A stratified random sample deals with situations where you have uneven distributions of people or you have the cluster sample issue.  

**Example**: We have a school with 300 students out of which 30 are minorities.
- In imbalanced scenarios like these, taking a random sample of 50 students is likely to produce a very small number of minority students.  It makes sense to take a separate sample of minority and majority students so we have a representative sample.
- Stratifications improves the distribution of your samples, and produces random samples that can be better for representation of minority groups.


## Convenience sample

A completely separate sample, which is just something that we happen to get.  In most survey studies, you get data from a portion of people that happen to respond.  Some people argue that these should be avoided but some people argue this allow designs that wouldn't be possible with random sampling.