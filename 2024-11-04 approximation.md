Relaxation:
- Relax some constraints of the problem, optimize the easier problem, which creates a feasible solution to the old problem
- Show that approximation ratio is better than (rounded OPT) / (easy OPT)
- Last time: metric travelling salesman problem using minimum spanning tree relaxation
	- Obtained 2-approximation
	- Christofides: 3/2 approximation
## 1 | $r_j$ | $\sum c_j$
- Scheduling on 1 machine
- Conditions: release times (cannot start job $j$ before time $r_j$)
- Objective: sum of completion times (average completion time)
- This makes the problem NP-hard :(

Simpler problem: 1 || $\sum c_j$
- Do short jobs first because its time adds to the completion time of all future jobs
- *Proof of correctness.* left as an exercise ✨ (b/c I don't wanna take notes)

What issue do the $r_j$ cause?
- Perhaps there's a job we want to run early that we can't do yet
- Intuition: do the shortest job that has been released
- But what if we start that job, and then a shorter one gets released? Ideally we'd be able to pause the current job.
- Observe: if we allow preemption (pausing jobs is ok), that becomes a relaxation of 1 | $r_j$ | $\sum c_j$.
	- Claim: "shortest remaining processing time first" (SRPT) is an optimal strategy
	- *Proof.* Consider an OPT preemptive schedule that is not SRPT
		- Then there's a moment when we are processing job $j$ even though job $k$ is available and is shorter
		- What we can do is "swap the role" of $k$ and $j$ to have $k$ finish completely before starting $j$

Rounding back up
- The hope is that there's enough "guidance" from the relaxed problem to solve the original problem
- Solution to the original problem: run the jobs in the same order (of ending time)
	- Take the preemptive OPT, and at time $c_j$ insert an additional $p_j$ spare time at this point
	- Run $c_j$ in this spare time (notice that we are are allowed to run in this time because $c_j > r_j$)
	- How much does this slow down job $k$?
		- If this extra time for $j$ delays $k$, then $k$ must have been completed after $c_j$ (b/c we're completing at $c_j)$, i.e. $c_j < c_k$
		- This means in the preemptive schedule, we ran all of job $j$ before $c_k$
		- That used up $p_j$ of the original timeline before $c_k$
		- So $c_k \ge \sum_{j \mid c_j < c_k} p_j$ (summation is over preemptive schedule)
		- This is exactly the sum of times we inserted before $c_k$
		- So before $c_k$, we inserted (at most ) $c_k$ additional processing time
		- Thus we have a 2-approximation

**Can we do better?**
Yes! Rounding and enumeration gives a polynomial approximation scheme (get within a factor of $1 + \varepsilon$ for arbitrarily close $\varepsilon$)
## General techniques for approximation
Vertex cover problem

Linear programming
- $x_i$ is an indicator variable for whether we take vertex $i$ or not
  $$x_i \in \{0, 1\}$$
- Objective: $\min \sum x_i$
- Cover:
  $$ x_i + x_j \ge 1 \quad \forall\, (i, j) \in E $$

Unfortunately we cannot specify $x_i \in \{0, 1\}$. So what if we just say $0 \le x_i \le 1$?
- Constraining $x$ to an integer does not work because our analyses only apply to continuous $x$
- Integer linear programming is NP-hard

LP relaxations of integer linear programs
- Take away the integrality constraint
- After we solve it, how do we round the solution?
	- What if we just round to the nearest integer?
	- This works because for every $x_i$ and $x_j$ on an edge $(i, j)$, at least one of them has to be $\ge 0.5$ so we get a vertex cover

Approximation ratio?
- Strategy (as usual): **compare value of the rounded solution to the optimum of the $x_i$**
- Each $x_i$ at most doubles (because only for $x_i \ge 0.5$ do we increase its value to $1$)
End up with a 2-approximation

Fun fact: this even works for weighted problems (i.e. each vertex has a cost)

**Can we do better?**
- Hastad showed that you can't do better than 7/6
- Dnur-Safra showed you can't do better than $10\sqrt5 - 21 \approx 36$
- Khet Minzne Safra showed you can't do better than $\sqrt2$
- Khot showed you can't do better than $2$ if the unique games conjecture is true
- $2 - O\left(\frac{1}{\sqrt \log n}\right)$ is possible
	- Powers the best known approximations for graph coloring

Complement of vertex cover is an independent set (no two vertices in the independent set have an edge between them)
- Independent set is known to be NP-hard to get within $n^{1-\varepsilon}$ for any epsilon
- Approximating the complement is very difficult! Even though we can approximate the original problem
## Facility location
- You want to open some facilities to serve customers
- Every facility costs $f_i$
- Assignment costs $c_{ij}$ of assigning customer $j$ to facility $i$
- Goal: open a set of facilities at costs $f_i$ then assign each customer to an open one at cost $f_{ij}$ while minimizing total cost

Note: this is NP-hard
We will focus on the **metric** case (i.e. triangle inequality holds) which makes

Integer linear program:
- $y_i$ indicator variable for whether we open facility $i$
- $x_{ij}$ indicator variable for whether we assign client $i$ to facility $i$
- Goal: minimize $\sum_i f_i y_i + \sum_{ij} c_{ij} x_{ij}$
- Constraints:
  $$ \sum_i x_{ij} \ge 1 \quad \forall\ j $$
  because that constraints one of the $x_ij$ to be one
- We also need
  $$ x_{ij} \le y_i \quad \forall\ i,j $$
  because we cannot assign folks to closed facilities

Relax to regular linear program
- Take away the integer constraints, so now instead of $x_{ij}, y_i \in \{0, 1\}$ to $0 \le x_{ij}, y_i \le 1$
- Rounding to the nearest integer doesn't necessarily work because some of the $x_{ij}$ could be less than 1/2
	- As if we distribute the assignments of one customer over all the facilities
- Filtering:
	- We want to fully assign $j$ to some "partial" assignment $x_{ij}$ (i.e. we want to round **one** of the $x_{ij}$ up)
	- The cost of doing this is at most $c_{ij}$ if we take 0.001 -> 1
	- $C_j$ can be seen as a weighted average $\sum x_{ij} c_{ij}$ of the $c_{ij}$ cost
		- If we could assign at a cost close to the average, we'd be doing well
		- Concern: there are some $c_{ij}$ that are very large but are being scaled by small $x_{ij}$ which makes $C_j$ small
		- Claim: **at most** a $1/\rho$ fraction of the $x_{ij}$ "weight" can be on $c_{ij}$s that are bigger than $\rho C_j$. 
			- Throw this fraction away (set $x_{ij} \rightarrow 0$)
			- Also scale the $x_{ij}$ up by $1 - \frac1\rho$ to compensate and ensure the sum condition
			- Also scale up the $y_i$ by $1 - \frac1\rho$ so that they still exceed the $x_{ij}$
		- Now the remaining $x_{ij}$ are small, i.e. less than $\rho C_j$. If we assign $x_{ij} \rightarrow 1$, we increase by a factor of at most $\rho$
		- Now we can assign each $j$ to **any** open $i$ with $x_{ij} > 0$ and pay at most $p \sum C_j$
			- [**WAIT WHAT**] shouldn't it be $1 - \frac1/\rho$
		- Total assignments is $\le \rho \sum C_j$
		- Facility opening
			- If all the $y_i$s are small, we're screwed... so we need to combine facilities together
			- Find a "cluster" of nearby facilities whose total of $y_i$ is at least 1
			- Then open the cheapest facility in this cluster. Cost of this is $f_\text{cheap}$ which is
			  $$f_\text{cheap} \le f_\text{cheap} \sum_\text{cluster} y_i \le \sum_\text{cluster} y_i f_i, $$
			  which is in the objective function. We can therefore afford to open the cheapest facility in each of the clusters and pay the same as the fractional solution dictates.
