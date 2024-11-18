Min cost flow: Generalized version of max flow problem
- Add costs $c(e)$ on edges, which are in units of "cost / unit flow." Costs can be positive **or** negative (or zero)
- Define the cost of a flow as $\sum c(e) f(e)$
- Sending $f$ flow on $e$ creates a residual reverse arc
	- The cost of this should be $-c(e)$ (capacity on the residual arc represents removing flow on the forward arc)
	- Removing flow is equivalent to removing cost of that edge
- We may have use for parallel edges (e.g. if there's a small cost for a small-capacity edge and a big cost big-capacity edge)
- **Objective:** still send as much flow as possible,  
	- We can also ask how much cost to send a given amount of flow less than 
	- Reduction to this problem: put a bottleneck at the end, after the sink, with capacity == our limit and zero cost
- The min cost max flow problem can be seen as a generalization of shortest paths
	- To solve shortest paths using min cost max flow, send just one unit of cost
- **Variation:** Min cost circulation problem
	- There is no source or sink. A circulation is a flow that is balanced everywhere. Note that circulations are composed of only cycles.
	- Claim: min-cost circulation is always nonpositive b/c we can always just do no flow, which comes out to zero cost
## Reductions
Suppose we want a reduction to MCF (min-cost flow) i.e. we have a fast solution for MCF, can we solve min cost circulation?
- Add a dummy source and a dummy sink that aren't connected to anything\

Can we solve min cost max flow with min cost circulation?
- Connect $T$ to $S$ with an infinite-capacity, negative infinite cost edge (b/c then it's always worth it to send more flow from $T$ to $S$, since that decreases total cost by a huge amount)
	- In the best case, every edge connects from $S$ to $T$ and has max capacity, which if we have $n$ nodes and max cost $C$, then $-nC$ is sufficient as "infinity" because max path length is $n-1$.
- Another reduction? If we don't want huge cost edges
	- Suppose we have some max flow $f$ and some other min-cost max-flow $f^*$.
	- What's the difference? i.e. what is $f^* - f$? We've sent $f^*$ flow from $S$ to $T$ and sent the same stuff back, so we are left with a circulation (source/sink have no net flow)
	- Conversely: flow + circulation = flow.
	- Claim:  $f^* - f$ is feasible in $G_f$ (residual graph of $G$).
		- If flow is $f_e^* - f_e \ge 0$, then there is a flow of this value on edge $e$, but the capacity of $e$ in $G_f$ is $u_e - f_e$.
			- But $f_e^* \le u_e$, so $f_e^* - f_e \le u_e - f_e$.
		- Else, there is flow $f_e - f_e^*$ on the reverse edge, which has capacity $\ge f_e$.

Suppose we're given a starting $f_e$. Consider any feasible circulation $q$ in $G_f$. Then $f + q$ is a feasible flow in $G$. [**WHY??**]
- The cost of a new flow can be decomposed as $c(f) + c(q)$.

Thus to get max cost flow $f^*$, all we need to do is add min cost circulation in the residual graph and add it to any plain old max flow.
## Deciding optimality
Someone gives us a solution to min-cost max flow. How do we decide if it is optimal?
- Does there exist a negative-cost cycle on the residual graph? If so, we can add it and get an even lower cost for our flow.
- If there is no such cycle, we can prove by contradiction that there is no better cost flow.
- We can decompose circulations into cycles. The only way for a circulation to have negative cost is for there to be a cycle that is negative cost.
	- Conversely, if there is a cycle of negative cost, it is a circulation and can be added to the flow to decrease its cost.
	- This proves that a flow is optimal if and only if there are no negative cycles in the residual graph $G_f$.

Corollary: the same check works for min cost circulation
## Algorithms for min cost flow
### Cycle cancellation
Suppose we have a flow that is non-optimal.
- Augment by the flow until it is saturated on at least one edge.
- This destroys the min cost cycle on the residual graph b/c we destroyed an edge
This is known as a "cycle canceling algorithm," of Klein in the 1970s
- We will eventually get to an optimum ?? assuming integers
	- Suppose we are given $U, C$ (max capacity, max cost magnitude)
	- Improve the cost by at least 1 on every cycle cancellation
	- Min possible cost: $-mUC$ b/c there are $m$ edges and each edge has cost at least $-C$ and capacity at most $U$.
	- **Runtime:** about $O(mUC)$

How do we find negative cost cycles?
- Run a shortest paths algorithm (e.g. Floyd-Warshall, Bellman-Ford) and see if it fails
- Scaling shortest paths can run in $O(m\sqrt n \log C)$

Overall, this algorithm runs $O(nm^2 UC)$ or $O(m^2 \sqrt{n}\,CU\log C)$
### Economics
- We will show a different way of determining optimality
- Our current flow algorithm currently decides everything all at once
	- "This is communism. What if we let markets solve our problem for us?"
- Imagine supply of infinitely precious material (e.g. boba) at source $s$ and a demand at $t$
	- Offer large payment for boba at $t$ and give it away for free at $s$
	- This creates a market for boba at intermediate vertices
	- Price $p(v)$ for boba at $v$
		- Where do costs come from? They derive from shipping costs
		- Suppose we have a cost 10 to ship $s$ to $v$, $10, then you can buy boba for $10 at $v$
	- When is a set of prices "stable/correct"?
		- Suppose we have an edge $(v, w)$. There's a price at $v$, a price at $w$, and a cost of sending boba from $v$ to $w$
			- Denote these $p(v)$, $p(w)$, and $c(vw)$.
			- Conditions:
				- There is no capacity left from $v$ going to $w$.
				- Or $p(w) \le p(v) + c(vw)$ ("triangle inequality")

- **Definition.** "reduced cost" $c_p(vw) = c(vw) + p(v) - p(w)$. 
	- This is the cost to buy at $v$, ship to $w$, then sell to $w$.
- **Definition**. a price function is *feasible* for a (residual) graph if no residual arc has negative reduced cost.
- Observation: min cost flow is the same under the reduced costs as the original costs.
	- Why? Cost of cycles is the same due to telescoping.
	- The reduced cost of *every* $s-t$ path changes by $p(s) - p(t)$.
	- Meaning every max flow changes in value by (value) * (p(s) - p(t))

**Claim.** A circulation/flow is optimal if and only if there exists a feasible price function in the residual graph.
1. We must show that feasible prices in $G_f$ proves optimality.
	1. By definition, reduced costs given feasible prices $p$ are nonnegative on every residual arc.
	2. Since there are no negative arcs, there must be no negative cycles under reduced costs. By observation, there are also no negative cycles under the original costs.
	3. This means the original flow is optimal.
2. Next, we must show that if we have an optimal flow, there are feasible in $G_f$, i.e. there are no negative arcs.
	1. Intuition: set price function at $v$ to be $p(v)$, the min cost to ship boba to $s$. 
	2. Show that this price function is feasible (i.e. all reduced costs are nonnegative).
	3. This is trivial by triangle inequality