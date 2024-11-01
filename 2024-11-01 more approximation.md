## P || c_max
- One idea: greedily schedule large jobs first gives a 2 * OPT solution
- Another idea: check all $m^k$ arrangements of largest $k$ jobs, then for each arrangement, schedule the rest of the jobs greedily. Take the best.
	- We may assume the top $k$ are scheduled optimally.
	- Overall, requires $O(m^k n)$ time
	- If we want an $\varepsilon$-approximation, we need $k = m/\varepsilon$, so runtime would be $O(m^{m/\varepsilon} n)$ which is technically polynomial in $n$ but not $m$.
## Better algorithm
Assume optimum time $c_\mathrm{max}^*$ is known.

Create an algorithm that finishes in time $c_\mathrm{max}^*$. This is a "feasibility problem."
- Have we made the problem any easier? **No.** Because if we can do this, we can use it in a binary search to find the optimum.

Start with an easier problem:
- Suppose there are only $k$ distinct sizes of jobs
- Claim: we can use DP to solve
- An instance is just a count of the number of jobs of each size
- A **machine schedule** is the same (a count of the number of jobs of each size)
	- Extra constraint:
	  $$ \sum (\text{count of size}) \cdot \text{size} \le c_\mathrm{max}^* $$
- An overall schedule is a collection of machine schedules
- How many distinct instances?
	- Technically $\binom{n + k - 1}{k - 1}$, but we will be lazy and say $n^k$ instances exist
	- There are also at most $n^k$ distinct job sizes
- **Which instances can be feasibly scheduled by $s$ machines?**
	- Where $s$ starts at $1$ and goes to $n$
	- If $s=1$, check that total job length is at most $c_\mathrm{max}^*$.
	- Suppose we know which instances can be run on a set of $s$ machines.
		- For every $s$-feasible instance, adding any one machine type gives an $(s+1)$-feasible instance
		- There are $n^k$ $s$-feasible machines and $n^k$ machine types, so total $n^{2k}$ possible transitions
		- $m$ work per transition
		- $n$ values of $s$ to iterate over
		- Total: $O(mn^{2k+2})$ work to enumerate all workable instances

**How do we apply this?**
- Scale down the job lengths and and round down
- Issue: when you're rounding down, you lose a lot of precision
- "Geometric rounding"
	- If two jobs differ in size by a **ratio** that is $\le 1 + \varepsilon$, they are indistinguishable for a $(1+\varepsilon)$-approximation
	- In theory, something bad that could happen is job sizes of $1$, $(1+\varepsilon)^2$, $(1 + \varepsilon)^3$, etc.
	- All jobs of size $\varepsilon c_\mathrm{max}^*$ will be called "large" and everything else will be called "small"
	- We will carefully schedule large jobs and greedily schedule small jobs
	- To schedule large jobs, round each to closest larger power of $(1 + \varepsilon)$
		- The large jobs have size between $\varepsilon c_\mathrm{max}^*$ and $c_\mathrm{max}^*$
		- So there are at most $\log_{1+\varepsilon} 1/\varepsilon$ buckets here, which is the same as
		  $$ O\left(\frac{\log 1/\varepsilon}{\varepsilon}\right).$$
		  Runtime for this is
		  $$ n^{\tilde{O}(1/\varepsilon^2)} \cdot m. $$
		  For every given $\varepsilon$, this is a polynomial algorithm in $n$, $m$, and $1/\varepsilon$
	- This gives an optimal solution to the rounded problem whose OPT is at most $(1 + \varepsilon) c_\mathrm{max}^*$.
	- It remains to schedule the small jobs greedily
		- Either the last job to finish is large, in which case we our time is $(1 + \varepsilon) c_\mathrm{max}^*$
		- Or the last job is small, which means the runtime is at most
		  $$ (\text{avg load}) + (\text{small job}) $$
		  which again fits within the limits b/c it's at most $c_\mathrm{max}^* + \varepsilon c_\text{max}^*$.

Extra question: [**WHY IS SCHEDULING $k$ BIGGEST JOBS OPTIMALLY ENOUGH**]
## General idea
Find some "kernel" of the problem, solve it in brute-force time, and then fill in the rest without disturbing the solution much
## APX problems
**Relaxation** algorithms
- Heuristic: if something is too hard, work on something easier
### Travelling salesman problem
There is a graph with edge weights. We want to find a cycle that visits each vertex exactly once and returns to the start with minimum total edge cost.

- Just finding a cycle that visits every vertex once is NP-hard. It's the **Hamiltonian cycle problem**.
- **Metric TSP:** edge lengths are a metric (i.e. satisfies triangle inequality and is symmetric) and the graph is complete (i.e. exists edge between every pair of vertices)
	- This is equivalent to saying that we are allowed multiple visits to a vertex (b/c this optimizes triangle inequality) + graph is undirected

This problem is max-SNP hard, but we can do some interesting approximations.
### Relaxation
- Take hard problem and find easy problem that is similar (relax some constraints but introduce some approximation).
- Solve easy problem.

Note: feasible solution for hard problem is also feasible for easy problem, so optimum for easy problem is better than optimum for hard problem.

Let's consider the **metric** version of the TSP (visit every vertex once)
- To relax, turn this into minimum spanning tree ??
- Once we have an MST, we need to "round" it back into a path
- Observe that feasible paths are also feasible trees :)
- So MST <= TSP
- If we manage to stay close to the MST cost, we know that we're also close to the TSP cost
- Take a DFS on the MST, which might visit vertices multiple times, but it's ok to skip repeated vertices because triangle inequality ?
- The cost of this path is (at most) twice the weight of the MST because we traverse every edge twice

Christofides
- Perhaps there's a better way of making the tree Eulerian than doubling all the edges
- We can't take an Euler tour currently because there are several vertices with odd degree
	- Add edges until all degrees are even
	- Recall that we're working in a metric, so every edge is allowed
	- Add edges between pairs of nodes with odd degree
	- So we need a **perfect matching** between nodes of odd degree
	- Can there be an odd number of odd vertices?
		- **No**, because that would mean there's an odd number of edge endpoint, but we know that's even because every edge has two endpoints
	- What is the new cost of the tour?
		- MST + cost of matching
	- We want a min cost matching !!
		- Turns out min cost non-bipartite matching is tractable in polynomial time
		- Uses augmenting paths (blossoms)
	- Do we know that there's a cheap min cost matching?
		- We know that there exists the optimal TSP tour
		- We can shortcut the TSP tour to connect up the odd vertices using a cycle, take **every other edge** to get a matching
		- Cost of odd tour is at most TSP cost (by triangle inequality)
		- One of them costs at most TSP/2
	- So MST + cost of matching <= 3/2 TSP
### Recent progress
We can do an approximation of factor $3/2 - \varepsilon$, found by Madry
Held-Karp bound: technique that penalizes large degrees so that the MST has small max degree
- Conjectured to give a 4/3 approximation
Directed version (different edge costs going both ways, but triangle inequality still holds)
- Recent: $O(\frac{\log n}{\log \log n})$

Euclidean TSP has polynomial approximation scheme