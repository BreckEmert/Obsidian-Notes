![[Pasted image 20241017150121.png]]

#### apply() function
**input**: data.frame or matrix
**output**: n-1 dim if aggregate else transformed

**lapply()**: list apply.  The exact same as apply but returns a list instead of the equivalent object.
**sapply()**: simplified apply.  Returns a vector.  Will return a list if it cannot return a vector or matrix, hence the 'simplified'.
**tapply()**: computes a measure for each factor variable in a vector.  
	`tapply(X, INDEX, FUN)` 
	Arguments:
		- X: An object, usually a vector
		- Index: A list containing factor
		- FUN: Function applied to each element of x
	Example:
		`tapply(iris$Sepal.Width, iris$Species, median)`
		Computes the median of the length for each species

To determine the levels of a categorical variable, just do `table(var)`.

## Formula Syntax
https://stackoverflow.com/questions/8055508/in-r-formulas-why-do-i-have-to-use-the-i-function-on-power-terms-like-y-i

#### `^` Operator
- Defines interactions to a specified degree (does **not** create quadratic terms by default).
	- Example: `y ~ (x1 + x2)^2` expands to `y ~ x1 + x2 + x1:x2`
	- Note: `A^2` is interpreted as `A * A` (self-interaction), which simplifies to `A`. For explicit quadratic terms, use `I()`.
#### `*` Operator
- Expands terms to include both individual terms and interactions.
	- Example: `a * b` expands to `a + b + a:b`
#### `:` Operator
- Denotes an interaction between specific terms without including the main effects.
	- Example: `x1:x2` includes only the interaction term `x1:x2` without `x1` or `x2` as main effects.
#### `I()` Function
- Treats expressions "AsIs," preserving the term exactly as written, which is especially useful for quadratic or other transformations.
	- Example: `y ~ I(x1^2)` specifies `x1^2` as an independent quadratic term, preventing R from interpreting it as an interaction of `x1` with itself.
	- Required when you need polynomial terms (e.g., `I(x1^2)`, `I(log(x1))`) that R would otherwise simplify or interpret as interactions.
#### `.` Notation
- Represents all main effects from the specified dataset in a formula.
	- Example: `lm(y ~ .)` includes all columns in the dataset as main effects for predicting `y`.
#### `-` Operator
- Excludes specific terms from the formula.
	- Example: `y ~ x1 + x2 - x2` includes only `x1` in the model, removing `x2`.
	- Commonly used to exclude interactions or other terms while retaining main effects.

