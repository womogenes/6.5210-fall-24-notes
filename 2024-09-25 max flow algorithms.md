## Stuff we covered last time
- Max flow min cut theorem
- Augmenting paths
	- "A flow is a maximum flow if and only if there is no augmenting path"
	- "if and only if there is a **saturated s-t cut**, where the value of the cut equals the value of the flow"
### Augmenting path algorithm:
- Repeatedly find an augmenting path using BFS in $O(m)$ time and saturate it. Do this until there's nothing left
- Question: how many times must we augment before we reach optimum?
	- With integral capacities, all augmentations are integers
	- At least one unit increase per iteration
	- Runtime: $O(mf)$ where $f$ is the value of the flow for 0-1 capacities & no parallel edges
	- If $U$ is the maximum capacity of an edge, runtime becomes $O(mnU)$.
### Rational capacities
- Suppose numerator and denominator are all less than some value $U$.
- Find a common denominator (literally just multiply all the denominators) to scale all capacities up to integers.
- Solve the integral max flow problem.
- Scale down by the common denominator.

The issue is that the product of all denominators is on the order of $mn \cdot U^m$.
- With reals, it turns out that the augmenting path algorithm might approach a limit that is *not* the max flow.
### Greedy approach to augmenting paths
- Choose an augmenting path with maximal capacity (bottleneck)
- Questions
	- How?
		- Dijkstra's except length of path is the min edge length
		- We can also think of this as Prim's, expanding to new nodes that have big edges connecting to our current node
			- $O(m + n\log n)$, $O(m \log^* n)$, $O(m \log \log^* n)$ are all possible time bounds due to Fibonacci heaps etc.
			- Simpler approach: do a binary search on the value of the bottleneck edge (i.e. binary searching for the solution). It's $\log m$ for the binary search and $m$ searches, so $O(m \log m)$ to find best augmenting path.
	- Does this work well?
		- Let's think back to flow decomposition
		- Theorem: every flow can be decomposed to at most $m$ paths
		- Suppose the residual graph has value $f$
		- There exists a path in the residual graph with flow at least $f/m$ by Pigeonhole
		- This residual graph thus has a bottleneck edge with capacity at least $f/m$.
		- Thus the max capacity augmenting path has capacity at least $f/m$, reducing the remaining flow in the residual graph by $1-\frac1m$.
		- Claim: if max flow is $f$, then $t$ rounds of max augmenting path reduces the residual flow to
		  $$\left(1-\frac1m\right)^t f.$$
		  - Suppose we have integer capacities.
			  - Each augmentation is an integer, so once we get down to less than 1 remaining capacity in the residual graph, it must be zero, so we are done.
			  - Doing the math gives $t = O(m \log f)$.
			  - We know that $f \le nU$, so $O(m \log (mU))$ is the number of iterations we must do.
			  - We have $\sim O(m)$ time per iteration, so $\sim O(m^2 \log (nU))$ is achievable.

		- What about rationals?
			- Cast out denominators, which increases our max capacity to about $U^m$. This makes our runtime $O(m^3 \log (nU))$.
### Real numbers
- We can never get to a max flow

- This is **not** a "strongly polynomial algorithm" because the runtime depends on the size of the **numbers** in the input (not just structure).
- This **is** a "weakly" polynomial algorithm.
## Scaling algorithm for max flow
This is another weakly polynomial algorithm. It uses a trick called scaling.
- Developed by Gabow in 1985.
- But developed first by Dinitz in 1973.

The idea: apply the 0-1 case to general numbers.

Recursive idea:
- Round down the least significant bit by dividing everything by 2 and ignoring the fraction.
- Round down the input *and* the solution.
- i.e. get a decent lower bound on the problem by using some of the precision (i.e. use the rounded-down capacities which are integers, solve the problem, then do the problem again with the fractional parts left over).

- Suppose we did find such a rounded solution.
	- Double the capacities and the flow.
	- Add 1 to some capacities (the ones that were odd before)
	- Now the flow is no longer maximal... to make it maximal, augment.
- How many augmenting paths will we use to fix everything?
	- Al zero arcs (cuts?) in the rounded solution have capacity at most 1 in the residual graph of the unrounded solution.
	- The min cut of the rounded graph has residual capacity 0 in rounded solution and at most $m$ in the unrounded solution.
	- $m$ augmenting paths suffice because the capacities are back to 1 per edge. [**WHY?**] This takes $O(m^2)$ time b/c $m$ max flow and $O(m)$ BFS per increase
- Analysis
	- We do one iteration per bit, and there are $\log U$ bits, So total $O(m^2 \log U)$ runtime.

Real numbers
- We can bound error on real numbers using the scaling algorithm.
## Strongly polynomial algorithm for max flow
- We need a different notion of "progress towards the optimum."

What was it that allowed us to certify a max flow?
- An empty cut in the residual graph.
- How do we make it "hard" to get from $S$ to $T$?
- The distance from $S$ to $T$ — the cut from $S$ to $T$ being zero is the same as it being "hard" to get from $S$ to $T$.
- We can work to increase the distance from $S$ to $T$ until it becomes infinite.
	- Distance is measured in edge count — so longest shortest path is at most $n-1$ edges.
	- If we can get the longest shortest path to be length $n$, we have a distance of $\infty$ and we are done.

If we have a graph of unit capacities, and the min distance from $S$ to $T$ has distance $k$...then every flow path uses (at least) $k$ capacity.  So the residual flow is at most $m/k$ (b/c max flow is $m$).

### Shortest augmenting path
Edmonds & Karp, 1972

Intuition: if we augment on a shortest path, we destroy that shortest path by cutting out the min edge in that path.

How do we find the shortest augmenting path (in terms of edge counts)?
- BFS
How many shortest augmenting paths do we need to get to a max flow?
- Every time we find a shortest augmenting path 

Key claim:
- **Claim:** Under shortest augmenting paths, no vertex ever gets closer to $S$ (and same for $T$).
- *Proof.* Assume we have distances $d(v)$ before SAP and $d'(v)$ after.
	- Suppose that some vertices get closer. In other words, suppose $d'(v) < d(v)$ for some vertices.
	- Let $v$ be the *closest* to $s$ (after) amongst these vertices. In other words, it has the minimum $d'(v)$.
	- Let $w$ be the preceding vertex on the shortest $d'$ path from $s$ to $v$. Then,
	  $$d'(v) = d'(w) + 1.$$
	- We can also say that $d'(w) \ge d(w)$ because $w$ did not get closer to the source as a result of the SAP (since $v$ was the closest that got closer).
- *Subclaim.* $(w, v)$ had zero capacity (in residual graph) before the last augmentation.
	- If $(w, v)$ existed before, then $d(v)$ is at most $d(w) + 1$ because we could have gone from $w$ to $v$ in one step. But also $d(w) \le d'(w)$ because $w$ did not get closer. But we also know $d'(w) + 1 = d'(v)$. Summarizing:
	  $$ d(v) \le d(w) + 1 \le d'(w) + 1 = d'(v). $$
	  But this contradicts our definition of $v$. Meaning $(w, v)$ did have 0 capacity before the last augmentation.
	- The only way to create capacity is to have the augmenting path go through a reverse edge (in the residual graph).
	- The augmenting path went from $w$ to $v$. But the shortest path went from $w$ to $v$...so actually we could've canceled out the shortest path.

Conclusion: shortest augmenting path never decreases the distance from $S$ to any vertex or from $T$ to any vertex. In particular, it does not decrease the distance from $S$ to $T$.

**Lemma**. Within $mn/2$ shortest augmenting paths, the distance from $S$ to $T$ is at least $n$.
*Proof.* Each augmentation saturates an edge. Unsaturating an edge (by travelling the reverse edge) is the only way to make an edge come back.
- To saturate an edge again, we need $d'(v) \ge d(v) + 2$.
- We can only saturate an edge $n/2$ times before the distance to that edge gets to at least $n$ and you can't reach it anymore.

Algorithm runtime: $O(m^2 n)$ time. It is worse than the $O(m^2 \log m)$ runtime of weakly polynomial runtime algorithm.