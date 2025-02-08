## Odds Ratios
Example for naive OR calculations "What are the odds of someone who does NOT work in the IT industry being satisfied compared to someone who does (i.e. what is the odds ratio)?":
```r
q23 <- data.frame(
  Satisfied = c("Yes", "No", "No", "No", "Yes", "Yes", "No", "Yes"), 
  IT = c("No", "No", "Yes", "No", "Yes", "Yes", "No", "Yes")
)
q23

q23_table <- table(q23$Satisfied, q23$IT)
q23_table
epitools::oddsratio(q23_table, method="wald", correction=FALSE, rev="rows")$measure

# Manual
(1/3) / (3/1)

```
Add in `rev="rows"` if you want to do No/Yes instead of Yes/No.

LR formula to prob:
- LR formula to OR: e^(prediction formula with the obs plugged in)
- OR to prob:  OR/(1+OR)
```r
# B_0hat = 1 and B_1hat = -2 for a new obs X1 = 0.25
OR <- exp(1 - 2*0.25)
prob <- OR / (1+OR)
```