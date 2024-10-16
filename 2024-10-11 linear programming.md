What did we talk about last time?
- Feasible points
- The fact that linear programs can be bounded/unbounded
- Solution to (equality) linear program has size polynomial in the sizes of the original numbers and the number of constraints / number of variables

Duality
- Either $Ax=b$ has a solution or there exists a $y$ such that $yA = 0$ but $yb \neq 0$ (i.e. $yb = 1$, because we can scale $y$)

Forms for linear programs
- Standard form: minimize $cx$ such that $Ax = b$, $x \ge 0$
- Canonical form: maximize $yb$, such that $yA \le c$.

Where are optima?
- Vertex: feasible point that is not a convex combination of other feasible points
- Extreme point: a point that is the unique optimum for some objective function
- Basic feasible solution: a point which is feasible and basic — all linear equalities are tight at this point. Also, $n$ linearly independent constraints (*including* the linear equalities) are tight where $n$ is the number of variables

Any standard form LP with an optimum has one at a basic feasible solution.
- Proof: "rounding scheme" where we start at an optimum and add tight constraints until we have $n$
Corollary: if an optimum is unique, it must be a BFS because if it is not a BFS, we can turn it into one (thus violating uniqueness)
## Proving that the definitions of corner are unique
- Suppose we have a BFS that is not a vertex. Then it is a linear combination of $a$ and $b$ where both $a, b$ are optima. But this means we have a degree of freedom, meaning it's not a BFS, thus contradiction.
- Conversely, suppose we are not a BFS but _are_ a vertex.
	- The intersection of the tight constraints must be at least 1-dimensional because we don't have $n$ tight constraints. Thus we can take two points on either side of this 1-dimensional space and get a convex combination that produces the point, violating vertex.

Extreme points
- Want to show: all BFS are an extreme point.
- Take objective function to be the sum of all $x_i$ that are tight in the BFS. This sum is 0.
- Take any other $x'$ that is feasible; we claim it is not optimal.
	- Differs on some of the inequality constraints, [**WHY? is it not possible for another feasible solution to have the same entries that are tight and differ in other, non-tight entries?**]\
		- Suppose we have another point which is the same on the inequality constraints AND the equality constraints; since we have $n$ linearly independent equations, it must be the same as the original point.
	- i.e. some entries in $x'$ are nonzero. Since all feasible solutions have nonnegative entries, some entries in $x'$ are have 

Corollary: the optimum of a linear program can be written in polynomial space.
*Proof.* the optimum is tight at $n$ linearly independent constraints (i.e. $n$ rows of $A$, denote as $A'$). Then $A'x = b'$ for $n$ linearly independent rows.  
- So we have $x = (A')^{-1} b'$, which is of polynomial size.

**Algorithm.** Look through all $\binom{m}{n}$ subsets of $n$ constraints.
1. If they are linearly independent, solve for the point that's tight at all of them
2. Ensure this point is feasible
3. Evaluate the objective at this function
Runtime: $O(\binom{m}{n} \cdot n^3 \cdot \text{size of numbers})$
## Verifying optima
- Given a $k$, is there a solution of value $\ge k$? This is easily verifiable if we have the solution.
- Can we prove if there is *no* solution of value $\ge k$? i.e. can we give a certificate of that?
- Can we give a checkable lower bound on the optimum?

Standard form:
- Minimize $cx$, given that $Ax = b$ and $x \ge 0$.
- Imagine we take some $y$ as a linear combination of some of our equality constraints.
	- In other words, we take $(yA)x = yb$. (the idea is we recombine rows of $A$ to get a desired value on the RHS $yb$).
	- Suppose we get really lucky and $yA = c$. Then $cx =  yb$ for all $x$. So we're really trying to maximize $yb$ (which is a real number b/c $cx$ is also a real number). And actually $yb$ is constant! So that means all $x$ are the same objective ... which seems pretty uncommon.
	- So let's shoot lower—suppose we want a $y$ such that $yA \le c$. Then for any feasible $x$,
	  $$y \bullet b = y \bullet (Ax) = (yA) \bullet x \le c \bullet x$$
	  because all $x$s are nonnegative (that's why the inequality works). So $yb$ provides a lower bound on $cx$, the optimum.

To get the best lower bound, we want to optimize $yb$ (i.e. make $yb$ as big as possible).
- This means maximizing $yb$ such that $yA \le c$.
- This is known as the **dual linear program** to the original **primal linear program**.
- We have proven "weak duality" — the optimum of the dual is a lower bound on the optimum of the primal.
- The primal also provides a lower bound on the optimum of the dual problem.
- Also, note that the dual of the dual is the primal.

Some more things this shows us:
- If the primal problem $P$ is unbounded, then the dual $D$ is infeasible (upper bound on the dual can be as high as possible)
- If $D$ is unbounded, then $P$ is unfeasible because our lower bound goes up forever.
- It is possible that both problems are infeasible.
- It is also possible that $P$ and $D$ are both feasible & bounded.

## How tight are these bounds?
- Claim: If $P$ and $D$ are both feasible, then their optima are equal.
- We will show that the solution for $D$ is the same as $P$.
Procedure:
- Consider the dual problem minimize $yb$ such that $yA \ge c$.
- Build "walls" (hyperplanes) in the direction of the constraints, each formed by a column of $A$.
- Each column  $A$ is the normal vector to one of these walls.
- Orient $b$ straight up. Then drop a ball and let it fall into the lowest point of the constraints.
Why does the ball stop here (a point $y^*$)?
- There is zero net force acting on the ball here.
- Forces:
	- gravity: $b$
	- normal forces from constraints: $A_i$ at some magnitude $x_i$
- For net force to be zero, we need $\sum A_i x_i = b$, i.e. $Ax = b$.
- Notice that all the $x_i$ are nonnegative because our walls are oriented according to the $A_i$
- $x_i = 0$ if we are not touching wall $i$
- What we have shown is that $x$ is a feasible point for the standard form LP $Ax = b$, $x \ge 0$.
- $x_i = 0$ if $yA_i > c$ (because we're not touching the wall ?)
- Put another way, $x_i (y A_i - c_i) = 0$ because either $x_i = 0$ or $yA_i - c_i = 0$.
- Rewrite this as
$$\sum_i c_i x_i = \sum_i x_i (y A_i) = (yA) x = y (Ax) = yb. $$
Intuition: when we look at optimal solutions to the primal and dual, the only place we have nonzero primal variables is where we have tight constraints in the dual problem. This is the complementary slackness.

**Fun facts:**
- max flow min cut duality is an instance of LP duality
- Reduced costs (price function) in min cost flow is another example of LP duality
- Potentials used in shortest paths are also an instance of LP duality

Next week: proof of LP duality (rigorization of physics intuition) and derivations of results.