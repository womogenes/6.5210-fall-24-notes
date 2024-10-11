## Min Cost Flow
Last time we showed how to decide whether a min cost flow is optimal.
We have not discussed algorithms for finding min cost flow.

- Define $p(v)$ prices
- **Reduced cost of an edge** $c_p(vw) = c(vw) + p(v) - p(w)$.
- Observe that reduced prices don't change cycle costs
- They change _all_ $s-t$ path costs by the same amount
	- Thus shortest paths stay shortest
	- Min cost flows stay min cost flows
	- If all reduced costs are nonnegative in the residual function (easy to compute), then there are no negative cycles
## Algorithms for min cost max flow
Greedy?
- Assume we have a graph with no negative cycles. A min cost flow already exists w/ zero cost.
- Consider how to reach max flow:
	- Always augment by the shortest path (where distance is determined by the edge costs)
	- Somehow this works ??
		- **Claim.** Augmenting by shortest path never creates negative cost cycles. This is good b/c when we finish, we will have a max flow, but no negative cycles. Thus it will be a min cost max flow.
		- *Proof.* Suppose we _do_ create a negative cost cycle. Then some augmentation must have created a negative cycle.
			- It cannot be made by edges that existed entirely before we did our shortest augmenting path. So we must have created a new edge that gets used by the negative cost cycle.
			- The only way this happens is if the shortest augmenting path used this edge in the reverse direction.
			- But this is a contradiction b/c we could've taken the negative cycle and gotten an even shorter/cheaper shortest augmenting path.
		- *Proof 2.* By induction, there are never negative cycles. Why? Given that there are no negative cycles at the start, we can compute prices as distances from $s$
			- Triangle inequality says that $c_p(vw) = c(vw) + p(v) - p(w) \ge 0$ (remember, again, $c$ is defined by distance).
			- All edges $(v, w)$ on the path from source to sink have cost zero.
			- The costs of the residual arcs we create are also zero. This means we didn't create any negative residual cost arcs **under the reduced edge lengths** because $p$ is still valid under SAP.

### SAP in general graphs (assuming no neg cycles to begin with)
- Perform Bellman-Ford to compute shortest paths
- Integer capacities means $mU$ augmentations, and $O(m^2 nU)$ total. [**WHY?**]
Improvement
- Use reduced costs (claim: this doesn't change shortest paths)
- $\tilde{O}(m^2 U) = \tilde{O}(mf)$ because we can use Bellman-Ford to the initial reduction and Dijkstra's for all future SAPs and reductions.
### Negative cycles
- Let's think about just min cost circulations (equivalent problem)
- Approach 1:
	- Saturate all negative arcs with flow. This introduces imbalances at some of the nodes (excesses at some and deficits at others). Use a flow algorithm to ship excesses to deficits.
	- How do we know there's enough capacity to serve the excess back to the deficit?
		- There exists a feasible solution (i.e. min cost flow) to ship the excess to the deficit
		- Take the min cost flow of this feasible solution
	- Claim: original saturation + mi cost flow returning excess to the deficit is a min cost circulation of the graph.
	- [**QUESTION:**] How do we know that the original saturation doesn't put us in a suboptimal spot with regards to filling too many capacities?
	- **Analysis:** we can solve min cost circulation in $O(mf)$ where $f$ is sum of all negative arcs, which is $\le mU$. This runs in total $O(m^2 U)$ time.
## Scaling min cost circulation
Edmonds-Karp, 1972

Capacity scaling
- In each scaling step, double the current capacity and current circulation.
- After this, we have nonnegative reduced costs with some price $p$.
- Add 1 to certain capacities (rolling in the next bit)
- Some of these capacities might be on edges with negative cost.
	- Fix the negative arcs by saturating them, which creates supplies/demands, then fix this
- We only created negative arcs of capacity 1, which means the total excess/deficit created is $m$. Fixing this thus takes $O(m^2)$ time.
- Total $\log U$ iterations, thus $O(m^2 \log U$).
## Complementary slackness
- Prices/reduced costs
- Suppose we have infinite supplies of boba
	- Suppose reduced costs of an edge is positive
		- Shippers are not going to use this edge
	- Suppose the reduced cost is negative
		- The edge will be saturated
	- Suppose the reduced cost is zero
		- Can't say anything
- A flow with these characteristics has **complementary slackness** to the price function.
	- Claim: if we have complementary slackness, it implies optimality of min cost max flow
	- Claim: If we have a min cost flow, it will exhibit complementary slackness with distance function in the residual graph

Suppose we have a feasible price function for min cost circulation.
- Observe reduced costs.
	- Complementary slackness says we don't use any edge with reduced cost > 0, so we can delete those arcs.
	- CS says we use all edges with reduced cost < 0. This creates excesses/deficits, so we run max flow to give excesses back to deficits. We are only allowed to use arcs with reduced cost zero.

We will see more of complementary slackness when we do linear programming.
## Generalizations
- Tardos '85: Minimum mean cost cycle
	- "Scaling-like algorithm" — fix certain edges as fully saturated after some strongly polynomial amount of time
	- $O(m^2)$ runtime
- Orlin: Double scaling
	- Scales on both cost _and_ capacity
	- $O(mn \log C \log \log U)$ runtime