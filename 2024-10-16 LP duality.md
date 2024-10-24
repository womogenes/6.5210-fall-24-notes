What did we show last time?
- Ball proof
	- There exists a dual solution with the same objective value
	- Started with trying to minimizing $yb$ given that $yA \ge c$.
	- Argued: tight constraints correspond to nonzero $x_i$ "forces"
	- Concluded that all the $x_i \ge 0$ because physics
	- Concluded that $Ax = b$ because physics (normal forces net to gravity force)
- Complementary slackness
	- The $x_i$ are zero where the ball does not touch the wall
	- Written compactly, $(c_i - yA_i)x_i = 0$ because $c_i - yA$ is the distance from the ball to the wall [**WHY??**] and $x_i$ is the force of the wall on the ball
	- Rewrite as $c_i x_i = (yA_i x_i)$
	- Rewrite as $cx = yb$
	- If we have "complementary slackness" , i.e. $(c_i - yA_i)x_i = 0$, we get that $cx = yb$ so we're good

![[Pasted image 20241019181800.png | 400]]
## Formalize
Consider the optimum for the LP:
- minimize $yb$
- given $yA \ge c$

- Choose a maximal subset $S$ of linearly independent tight constraints. All constraints are satisfied if these are.
- Note that $|S| \le m$, where $m$ is the dimension of $y$, since the dimension of $S$ Is full
- Choose $A_S$ and $c_S$, matrix and vector, restricted to columns of $S$ (i.e. we select from rows of $A$ and $c$)
	- Each constraint is a column, so we're deleting some of the irrelevant columns.
	- The point is that $A_S$ has full column rank, and $yA_S = c_s$ (we just restrict to certain rows) because these are tight
	- If $yb$ is the minimum restricted to $yA_S = c_S$, we are done.
		- *Proof.* [**THINK ABOUT IT**]
	- Claim: if we prove strong duality for this LP, we will have also proven strong duality for the original LP.
- If we do this, we have a subset of variables $x_S$ such that $yb = x_S c_S$.
	- Recall that the dual problem has $A_S x_S = b$
	- For every variable in the original problem, you get a constraint in the dual problem, and for every constraint in the original problem, you get some 
- The other way
	- Suppose we've found some $x_S$ such that $A_S x_S = b$ and $x_S \ge 0$.

Now we just need to give a proof of strong duality where the columns of $A$ are independent.
- Claim: there exists an $x$ such that $Ax = b$.
- *Proof.* If this is false, then there exists a $z$ such that $zA = 0$ but $zb \neq 0$. Assume without loss of generality that $zb < 0$ (can always flip sign as needed).
	- Consider a $y' = y + z$. It is feasible for the primal LP because $
	  $$y' A = (y + z) A = yA + zA = yA.$$
- $y'b = yb + zb < yb$, which is better than what we had originally. This is better than we had originally.
  
- Claim: $yb = cx$.
	- *Proof.* We know that $Ax = b$ and $yA = c$. Then
		$$ yb = y(Ax) = (yA)x = cx. $$
- Claim: $x \ge 0$.
- - *Proof.* If $x_i < 0$, we have $c' = c + e_i$. Consider a $y'$ solving $y' A = c'$ because $A$ has full column rank.
## How to find
**Corollary.** Finding a feasible solution (in general) is as hard as finding an optimal one
- Given an LP "minimize $yb$ given $yA \ge 0$" that we're trying to optimize, we're trying to find $y$ such that $$yA = c, Ax = b, x \ge 0 \text{ and } yb = cx.$$
- Just feasibility is as hard as optimization.
## Applications of strong duality
### Rules for taking the dual
Variables are $x_1, x_2, x_3$, constraints are defined by $c_1, c_2, c_3, A_{11}, A_{12}, \dots$
- $x_1 \ge 0$, $x_2 \le 0$, and $x_3$ unrestricted in sign

Dual:
- Maximize $y_1 b_1 + y_2 b_2 + y_3 b_3$
- $y_1$ is unrestricted in sign
- $y_2$ is $\ge 0$
- $y_3 \le 0$

Big matrix of constraints, first of which is
$$ y_1 A_{11} + y_2 A_{21} + y_3 A_{31} \le c_1. $$
Duplicate for $c_2$ and $c_3$.

Cleaning it up:
- We can make a table where the primal is on the left and the dual is on the right.
- Assume the primal problem is a minimization problem and the dual problem is a maximization problem
- The primal problem has some constraints and some variables
### Examples
Tip: If we're stuck on an LP, we can take the dual to see if we get any insight.

**Shortest paths**
- Intuition: model graph with physical ropes.
- $d_j - d_i \le c_{ij}$ which is to say the triangle inequality.
- "Separate $s$ and $t$ as far as possible, subject to the triangle inequality constraints."
What does the constraint matrix look like?
- There is a constraint for every edge of the graph. Thus $m$ rows.
- There is a variable $d_i$ for every node $i$. Thus $n$ columns. We have a tall skinny matrix.
- The only entries are $1$, $-1$, and $0$
The dual problem?
- We have a variable per edge and a constraint per vertex. What do these look like?
	- $y_{ij}$ is the variable for edge $(i, j)$
- Objective: minimization
	- Minimize $\sum e_{ij} y_{ij}$
	- Naturally $\le$ constraints in a maximization problem correspond with nonnegative variables.
- What are the constraints?
	- There's a $1$ in the matrix whenever the node for that column has an incoming edge on the row.
	- We know
	  $$ \sum_{i \rightarrow j} y_{ij} - \sum_{j \rightarrow i} y_{ij} $$
	  is equal to
	  - $+1$ if $j=t$
	  - $-1$ if $j=s$
	  - $0$ otherwise.
- This is a min cost max flow problem where $y_{ij}$ is the flow on edge $(i, j)$, and we restrict the source to $1$ outgoing flow and the sink to $1$ incoming flow.

