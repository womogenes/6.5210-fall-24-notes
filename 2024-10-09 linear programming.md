## General form of linear programming
- List of variables (as a vector, $\vec{x}$)
- List of constraints (linear equalities and inequalities $\ge, \le$ — note that we do not allow strict inequalities)
	- A single constraint can be seen as a dot product
- $\vec x$ is **feasible** if it satisfies all the constraints
- We say the linear program is feasible if there exists some feasible point for it
- We say that $\vec x$ is optimal if it optimizes a certain **linear objective function** over all feasible points
- Linear programs my also be **unbounded** if there are points of arbitrarily good objective value

Note: max flow can be formulated as a case of linear programming where the variables are the flows across the edges

Claim: every linear program is either:
- unfeasible
- has an optimum
- unbounded
## Notation
### Canonical form
Maximize $c^T x$ subject to the constraint $Ax \le b$, where the inequality needs all rows to hold
- $x$ is our vector of variables
- $c^T$ is our objective weights, termed the **objective**
- $A$ is a matrix of constraints, termed the **constraint matrix**. Each row of the matrix is a linear function on $x$, and $b$ contains the bounds on those 
Claim: every linear program can be transformed into canonical form
	- *Proof.* Move all the $x$ terms to one side and multiply by $-1$ as necessary

This is actually not the best form for algorithms. Thus we introduce the standard form.
### Standard form
Minimize $cx$, given that $Ax = b$, subject to the constraint that $x \ge 0$.

Claim: every linear program can be transformed into standard form.
- Introduce "slack variables" $s_i$—one for each row—and transform $a_i x \le b_i$ into $a_i x + s_i = b_i$ and $s_i \ge 0$. Basically we're saying $s_i$ is the amount by which $a_i x$ is less than $b_i$.
- Turn $x$ into $x^+$ and $x^-$, where $x = x^+ - x^-$, but these two variables are always nonnegative and one is zero while the other is positive.
## What does a solution look like?
- Can we write our solution down? Are they necessarily rational with not too many bits?
- Can we verify optimal solutions?
- Can we find a feasible point?
- Can we prove that there is no feasible point?
### Linear equalities
$Ax = b$
- When do we have a solution?
	- Easy case: square matrix
	- If we have a solution, it is easy to verify
- Can we construct a solution?
	- Yes, Gaussian elimination
- Equivalent statements regarding this linear system:
	- $A$ is invertible iff it has nonzero determinant iff it has linearly independent rows/columns
	- iff $Ax = b$ for all $b$
### Detour: sizes of numbers
- $n$ has size $\log n$ (bit-size)
- A rational number $p/q$ can be represented using $\log p + \log q$ bits
- size(x + y) = 1 + max(size(x), size(y))
	- [**HOW DOES THIS WORK FOR RATIONAL NUMBERS?**]
- size(x\*y) = size(x) + size(y)
- n-vector size is n * size(entries)
- n x m matrix: size n\*m * size(entries)
Our runtime will depend on the sizes of the numbers.
### Back to linear equalities
What is the size of $A^{-1}$?
- Definition: $A^{-1} = \frac{1}{\det{A}} \cdot (\text{cofactor matrix})$.
	- What is the size of the determinant of $A$?
	- We can use the permutation definition of the determinant:
	  $$ \det(A) = \sum_{\pi\ n \rightarrow n} \text{sign}(\pi) \cdot \prod_{i=1}^n A_{i,\pi(i)} $$
	  The product is of $n$ numbers, so $n \cdot \text{size of }A_{ij}$ is the size of the product.
	- We have $n!$ terms in the sum, so size is $n \cdot \text{size of } A_{ij} + \text{size}(n!)$
	- Size of $n!$ is $n\log n$, thus the output is still polynomial in size relative to $n$ and the number of inputs.
	- Essentially we have $n \log n$ size for each of the entries in $A^{-1}$. Space grows by a factor of $n$.

Can we show that there is *no solution?* When $A$ is not necessarily square?
- It's possible to check through Gaussian elimination
- But can we prove that there is no solution faster? i.e. can we find a witness?
- If $Ax = b$, then $b$ is spanned by the columns of $A$.
- If we want to show that there is no solution, we need to show that $b$ is not spanned by the column space of $A$.
- The set of points $\{Ax\}$ is a linear subspace of $\mathbb{R}^n$, and we want to show that it does not contain $b$.
	- Look at the projection of $b$ onto this subspace and verify that $b$ does not equal its projection. In other words, $b$ has a component not in the subspace $\{Ax\}$.
	- How this writes out: no solution to $Ax = b$ iff there exists $y$ such that is perpendicular to $A$ but not perpendicular to $b$.
	- *Proof.*
		- If $b$ is in the columnspace of $A$, then $yA = 0 \implies yb = y(Ax) = (yA)x = 0$.
		- If there is no $x$ such that $Ax = b$, then columns of $A$ do not span $b$, and part of $b$ is perpendicular to the subspace. Let $y$ be that perpendicular component.

Is $y$ of polynomial size?
- If $Ax = b$ is infeasible, we want to find $y$ such that $yA = 0$ and $yb > 0$. If we scale it, we get $yA = 0$ and $yb = 1$.
- But this is just another linear system! So $y$ is indeed polynomial size in the size of $A$'s entries. i.e. if we can solve
  $$ y \binom{A}{b} = \binom{0}{1}, $$
  and if this solution exists, we have proof of infeasibility.
## Geometry
- Canonical form: $Ax \le b$ is an intersection of finitely many halfspaces (halves of $\mathbb{R}^n$), i.e. a polytope
- Standard form is a subspace which is bounded by the $x_i \ge 0$ hyperplanes.
- Both formulations are convex. If $x, y$ (vectors) are feasible, then so is $\lambda x + (1 - \lambda) y$ for $0 \le \lambda \le 1$. Another way to put it is, the line connecting any two points in the polytope is also in the polytope.

What do optima look like?
- Since our objective is a linear function, its gradient points in a direction, so we can keep pushing a solution along this gradient until it hits a vertex.
- Question: what characterizes "corners"?
	- Vertex:
		- a feasible point that is not a convex combination of other feasible points
		- extreme point: a **unique** optimum for **some** objective
		- "basic feasible solution":
			- At $x$, a constraint of the form $ax = b$ or $ax \ge b$ is **tight** or **active** if $ax = b$.
			- For an $n$-dimensional linear program, a point is said to be basic if all equalities are tight and $n$ linearly independent constraints are tight.
			- Put another way, $x$ is the intersection of $n$ linearly independent constraint planes
			- This implies that $x$ is the **unique** point at this intersection
			- A **basic feasible solution** (BFS) is a solution that is basic and feasible
	- Claim: these three definitions are equivalent

Claim: any standard form LP with an optimum has one at a BFS
- *Proof.* Suppose that there is a non-BFS optimum at $x$. Then there are $< n$ tight constraints at $x$. This means there's at least one degree of freedom! (can be proved rigorously)
	- This degree of freedom gives us a line, and we can move along the line while maintaining the tight constraints and staying feasible (for a bit).
	- This objective function cannot increase in either direction because we assumed that $x$ is optimal.
	- But it can't decrease in any direction either because it'd increase in the other.
	- This implies the whole line is optimal.
	- Move on the line in a direction that decreases some positive $x_i$. We can keep doing this until we hit some constraint—one must come eventually because $x_i \ge 0$ (recall that we're working on standard form).
	- Iterate this at most $n$ times, and we will be at a BFS.

- Note: it is possible to have an LP in canonical form that does not have an optimum at a BFS.
	- For example: variables are $x, y$, optimization function is $0x + 1y$ (maximize it), and we want to maintain $y \le 1$.

- Corollary: if $x$ is a feasible point, we can find a BFS whose objective is at least as good
- Also, if the optimum is unique, it is a BFS !!