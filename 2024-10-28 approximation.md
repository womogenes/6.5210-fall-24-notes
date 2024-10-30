Options for NP-hard problems:
1. Develop a non-polynomial time algorithm with provable bounds
2. Consider special cases of the problem
3. Assume random inputs, discuss "expected" runtime
	1. e.g. Hamiltonian path problem
4. Settle for non-optimal answers (our objective)

**Definition**. Optimization problem with:
- instances $I$
- a set of feasible solutions $S(I)$
- a value function $f : S(I) \rightarrow \mathbb R$
Minimize $f(\bullet)$, called $\text{OPT}(I)$

e.g. Bin Packing problem
- Instance: set of item sizes $s_i$ for $i=1\dots n$
- a solution assigns each item to a bin, where bin cannot exceed size 1
- use minimum number of bins

Technical assumptions
- all inputs and the range of $f$ are rational
- assume inputs are of polynomial # of bits
- we want algorithms that are polynomial in the bit sizes

NP-hardness:
- An optimization problem is NP-hard if some NP-hard decision problem can be reduced to it
- Allow general Turing-reducibility

Definition of approximation algorithm:
- Any algorithm that gives a feasible solution
- Challenge: what constitutes a "good" approximation algorithm?
## Absolute approximation
$A$ is a $k$-absolute approximation algorithm if, for every input $I$, the diff. between output and real answer is at most $k$.
- In this class, **we want constructions** (not just the answer).

Example: graph coloring
- Given graph as input. Color each vertex such that no two neighbors have the same color
- Minimize # of colors
- Turns out this is really NP-hard :(
Planar graph coloring is easy to approximate though b/c at most 4 colors are needed.
- Easy to 5-color (allegedly)
- Easy to 2-color if possible
- Easy to 1-color
2-absolute approximations are thus easy

Edge coloring
- There exists a 1-absolute approximation which is constructive and runs in poly time
## These are rare because scaling
Absolute approximations usually only exist when the optimum is $O(1)$.
- Can usually prove impossibility via a "scaling" or "amplification" argument

**Knapsack**.
- Items with sizes $s_i$ and profits $p_i$ and a sack of size $B$
- Fit the maximum profit (without reuse), fitting within the size
- WLOG inputs are integers b/c otherwise we can multiply out denominators

Now suppose we were able to find a $k$-absolute approximation to the knapsack problem. 
- **Claim.** We could use this to solve knapsack exactly
- *Proof.* Scale up the problem, i.e. multiply all profits and $B$ by $(k+1)$. The value of the error grows by $(k+1)$ too, which is an issue.

**Independent set / clique.**
- Given a graph $G$, find a maximum independent set of vertices (i.e. no 2 vertices are adjacent).
- **Claim.** We could use this to solve exactly
- Algorithm finds a solution which almost as big as the optimal solution
- Make $k+1$ copies of the graph, which multiplies the optimum by $(k+1)$. Again, a problem.
## Relative approximation
- An $\alpha$-optimum solution is a solution of value between $\frac1{\alpha}$ and $\alpha$ times OPT
- We say an algorithm has an **approximation ratio** of $\alpha$ if it yields an $\alpha$-approximation on every input

**How do we prove that something is an $\alpha$-approximation algorithm?**
- We use lower/upper bounds on OPT
## Greedy algorithms
- Build a solution in a series of steps, greedy takes what looks like the best step at each opportunity

**Max cut problem**
- No source and sink, just break the graph into two pieces
- Find a cut that has the maximum number of edges crossing from one set to the other
- Greedily grow the cut (add it to whichever side increases the value of the cut more)
	- Each vertex has a number of neighbors on each side. Add it to the side with less neighbors

Analysis
- One upper bound is $m$
- When we place each vertex, we add at least half of its incident edges to the cut
- Since all edges get considered at some point, over all the steps, at least $m/2$ edges are cut
- Gives a $2$-approximation

**Set cover problem**
- Given a collection of items and a collection of subsets
- Want to find a subcollection of subsets that "covers" all items, i.e. union is all the items
	- Goal: use fewest possible # of subsets
- Greedy algorithm: repeatedly takes the set that covers the most uncovered items

We will not develop a lower bound, but will rather compare to OPT = $k$
- At the start, there are $n$ uncovered items
- By averaging, there exists some set that covers $\ge n/k$ items in the first round
- Claim: after $r$ rounds, there are less than $(1 - 1/k)^r n$ uncovered items
- After $O(k \log n)$ rounds, we will have covered everything
This gives an $O(\log n)$-approximation

**Vertex cover**
- Given a graph
- Find fewest possible vertices that cover all edges

Algorithm:
- Find an uncovered edge and add **both** of the ends to the cover
- Each each step, we reduce OPT by 1 because we know for every edge that **at least one** (and possibly both) of its endpoints are in OPT

**Scheduling theory**
- $m$ "machines" that can run a collection of $n$ jobs with processing times $p_j$
- Goal: assign each job to a machine to finish all the jobs as quickly as possible
- Machines are all identical, so the same job takes the same job on all machines

Greedy idea:
- One at a time, assign a job to the currently least loaded machine
- Graham's algorithm

Analysis
- Lower bound: total load divided by $m$ (but this is bad b/c it would be way below OPT)
- Largest job size is also a lower bound (this is also terrible)
It turns out that if you use both of these bounds, things work out b/c one of the bounds is always good
- Consider final max-load machine
	- How did it get here? Had old load $L$ and then we added $p_j$
	- We added the job to this machine b/c all other machines had load $L$
	- It follows that the avg. load is at least $L$
	- Thus OPT $\ge L \ge$ avg. load
	- We added $p_j$, which is at most the max job length
	- Our final length is at most $L + p_j$ which is at most OPT + OPT = 2 * OPT
	- More careful analysis gives a $(2 - 1/m)$ * OPT -factor approximation

If you first sort by long jobs first, it achieves a 4/3-factor approximation.