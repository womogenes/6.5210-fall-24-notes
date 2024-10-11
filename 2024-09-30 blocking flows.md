Last time: shortest augmenting path
- Pick shortest path from $S$ to $T$ to augment on, b/c this only increases the distance from $S$ to $T$
- $O(m)$ time BFS to find augmenting path
- Only $O(mn)$ shortest augmenting paths to make the distance from $S$ to $T$ infinity

Gives: $O(m^2 n)$ strongly polynomial max flow algo.

**Previous algorithms**
- Scaling: $O(m^2 \log U)$
- Unit capacities: $O(mf) = O(mn)$

**Thought:** A lot of the time in shortest augmenting path, the computations in the BFS should be able to be reused.
## Blocking flows (Dinitz)
- Use one shortest path calculation to destroy **all** shortest paths
	- Then the $s-t$ distance increases strictly instead of just being non-decreasing
	- $n$ blocking flows suffice b/c the shortest path is of length at most $n$
- Define an **admissible** graph to be a BFS of the residual graph
	- Has layers of vertices by distance from $S$
	- In this graph we focus on **admissible edges**, which go forward from less distance to greater distance
	- The shortest paths in this graph are composed of only admissible edges (and all such paths are admissible)
- Define a **blocking flow** as such:
	- A flow that saturates at least one edge on **every** admissible path
	- Note: this is a "maximal" flow (i.e. you can't add more flow at this local point), but it is not necessarily a **maximum** flow [**WHY?**]
	- Claim: adding a blocking flow increase
	- s the distance from $S$ to $T$
	- *Proof.* You've killed at least one edge on every shortest path, so you can't use any of these shortest paths anymore. But what if it creases **new** shortest paths?
		- Augmentation does create new arcs (paths through residual graph), but they are **backwards** arcs
		- New shortest paths would have to use vertical or backward edges, but this cannot decrease the distance.
- What remains?
	- We need to actually find blocking flows.

## Finding blocking flows
### Unit capacity
- Start a DFS from $S$ (only going along admissible edges, i.e. those that go forward) then augment on it. This is called a "**block**" step. Then keep going till we get to $T$. Keep doing this until there are no more paths left.
- We might hit a dead end at some vertex $v$; we reach $v$ through the greedy process, but then there are no arcs leaving $v$. Once there are no edges leaving $v$, we can effectively delete $v$ b/c all the paths passing through $v$ are already deleted.
- Back up to previous $w$, so we can delete the edge $(w, v)$.

### Runtime analysis
- Advances
	- Every advance is followed by a retreat or going to $T$.
	- Work here is same as block + retreat work
- Retreats
	- At most $m$ retreats because each retreat destroys an edge
- Blocks
	- Takes amount of time required to augment a path. Each path is at most length $n$, so augmentation/block is $O(n)$ time.
	- There are at most $f$ blocks (still talking about the unit case), making it $O(nf)$.
	- If we use $k$ edges per block, we delete $k$ edges ... so there are $O(m)$ blocks.

A blocking flow takes $O(m)$ time. There are $n$ blocking flows, so $O(mn)$. Improvements?
- This method allows parallel edges
### Better analysis
Suppose we have $m$ edges and so far have done $k$ blocking flows.
- The $s-t$ distance is at least $k$ now because blocking flows increase distance.
- Path decomposition implies that every flow path uses $k$ edges...so there's only room for $m/k$ flow remaining.
- This means we need to do at most $m/k$ more blocking flows to finish.
- Conclusion: $k + \frac{m}{k}$ blocking flows suffice in unit-capacity graphs. Setting $k = \sqrt m$ means we have $O(\sqrt m)$ blocking flows. The runtime turns from $O(mn)$ to $O(m^{3/2})$ (for unit capacity graphs).
## General capacities
- Advance/retreat/block structure still works
- Still true that advances <= retreats + blocks
- Still true that we do at most $m$ retreats
- What about blocks?
	- What we assumed for unit flows was that an admissible path, once destroyed, destroys all the edges.
	- But now blocks might only destroy one edge. Solution: charge the $n$ work used to do the block to that edge that got deleted.
		- There are $m$ edges and $n$ work per block, we have $O(mn)$ block work total.
		- There are $n$ blocks [**WHY?**] so $O(mn^2)$ total.
### Improving on this for unit capacity graphs
- Each blocking flow spends $m$ on retreats --> $O(mn)$ total overall
- Each block costs $n$ and adds one to the cost of flow, so $O(nf)$ total block work
- Total: $O(mn + nf)$ time for max-flow.

Intuition: $mn$ time is spent organizing the admissible graphs, etc... only $n$ time is spent doing each augmentation.
## Scaling
- Scale one bit at a time (i.e. solve the problem for one particular bit). i.e. solve for a most-significant bit, then shift everything left one bit and add some capacity to particular graphs
- Whenever we roll in a new bit...
	- Scaling increases this capacity by at most 1 per edge, which is at most $m$ total.
	- Thus the new max flow is $\le m$ per bit.
	- This is $O(mn + nf) = O(mn + nm) = O(mn)$.
- Thus the total cost is $O(mn \log U)$.
### Can we make this strongly polynomial?
[**QUESTION:**] Is this optimizing the finding of a singular blocking flow, or does it optimize things between finding blocking flows?

- The thing that makes this expensive is the $O(n)$ cost of augmentation.
	- One thing we might improve on is constructing new admissible paths from old ones.
	- Keep pieces of admissible paths
	- We want to be able to **link** roots of trees to nodes in others
	- Something else we want to be able to do is to **cut** edges when we saturate edges
	- We want to be able to **teleport** to the root (if we can jump forward to roots of trees)
	- On augmentation, we need to be able to **decrease** the capacity on all edges from the root to $S$...
	- We need to be able to find the **min** capacity

Sleator-Tarjan invented a data structure that does the above in $O(\log n)$ time. Wow! They're called link-cut trees
- Gives $O(m\log n)$ time per flow
- $O(mn \log n)$ time max flow algorithm.
## Flow decomposition barrier
Orlin figured out how to do $O(mn)$
- Goldberg-Rao: $O(m^{3/2} \log U)$
- Push-relabel algorithms (Goldberg and Tarjan) — allow things to be out of balance sometimes
	- This wins in practice
2010s went crazy
- Continuous optimization and linear algebra
- Johnathan Kelner, Aleksander Madry (gradient descent & newton's method)
- Electrical flow problem
- 2022 paper: "Max flow in almost linear time"
	- Depends on some $\varepsilon$ depending on how close you want to get to the answer
	- 109 pages ...