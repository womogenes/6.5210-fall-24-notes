- Find a cluster of facilities $\{ i \}$ with their $\sum y_i \ge 1$ 
- Open cheapest facility on cluster and pay at most $\sum f_i y_i$  within the cluster
- Recall that $C_j = \sum_i x_j c_{ij}$. it embodies the "average" cost of connecting $j$
- To build a cluster:
	- Choose $\min C_j$ 
	- Closes all his fractionally connected facilities $x_{ij} \ge 0$
	- Open cheapest facility in cluster and close all other facilities. Then assign the clients who become sad to our just-opened cheapest facility.
	- This is ok for the sad clients because the cost of reconnecting is at most $3\rho C_j \le 3\rho C_{j'}$ by the filtering rule.

Putting it together
- Each client $j$ is connected at cost $\le 3 \rho C_j$
- If we set
  $$\frac{\rho}{\rho - 1} = 3\rho,$$
  i.e. $\rho = 4/3$, then the facility opening cost is multiplied by 4 and the client connection cost is also multiplied by 4.
- This gives a 4-approximation.

Further research
- Exists a 1.488 approximation
- Cannot do better than 1.463 unless P=NP
## MAX-SAT (CNF)
Collection of OR "clauses" over boolean variables & negations
- Satisfy as many clauses as possible with one assignment

One technique: try a random assignment
- A random assignment is false with probability up to $2^{-k}$ for $k$-variable clauses (since they're all OR clauses)

LP relaxation
- Formulation of LP: create a variable $y_i$ for every literal
- Create a variable $z_j$ per clause that is 1 if the clause is true
- Maximize $\sum z_j$
- Constraints:
	- $z_j \le \sum_{i \in C_j^+} y_i + \sum_{i \in C_j^-} (1-y_i)$
	- For positive variables, $y_i$ is good if it is 1
	- For negative variables, $y_i$ is good if $1 - y_i$ is 1

"randomized rounding"
- To make the $y_i$ integers, we make them random variables that take on 0 and 1 with different probabilities
- Set $\hat y_i = 0$ with probability $1 - y_i$ and $\hat y_i = 1$ with probability $y_i$
Expected value of constraints is conserved...but is it always conserved?
- We get some distribution of the $z_j$ :/ but how do we know what it is?
- Define a series of numbers
  $$\beta_k = 1 - \left(1-\frac1k\right)^k$$
  The series goes $1, 0.75, 0.704, \dots$
- We are interested in $\mathbb{E}[\sum z_j]$, which, thanks to linearity of expectation, is
  $$\sum \mathbb{E}[z_j] = \sum \text{Pr}[z_j = 1].$$
  Now note that
  $$\Pr[z_j = 1] = 1 - \Pr[z_j=0] = 1-\Pr[\text{all }\hat{y_i}\text{ are zero}].$$
  This becomes
  $$1 - \prod_{i \in C_j} (1-y_i).$$
  In the worst case, all the $y_i$s are equal because that minimizes $\Pr[z_j = 1]$. In particular, we get
  $$y_i = \text{value} \cdot \frac{z_j}{\#\text{ of variables}},$$
  which gives
  $$\Pr[z_j = 1] = 1 - \left(1-\frac{z_j}{k}\right)^k.$$
- **Claim.**
  $$ 1 - \left(1 - \frac{z}{k}\right)^k \ge \beta_k z.$$
  *Proof.* By definition of $\beta_k$, and calculus.

When we do rounding, we get a $\beta_k$ approximation for any clause of size $k$. The $\beta_k$ tend towards $1 - 1/e$, which is actually better than $1/2$.

**Better approach:** run both and take the better outcome.
- We only need to look at the average of (random assignment) and (randomized rounding) and take he 0better one.
- Clause of size $k$:
	- Method 1 delivers $1 - 2^{-k}$ approximation
	- Method 2 delivers $(1 - (1 - 1/e))^k$ approximation
	- The average is at least 3/4.
## Fixed parameter tractability
Divide instances into classes via a natural parameter, then bound hardness in terms of this parameter.

e.g. vertex cover
- Parameter: $k = \text{size of OPT}$
- If $k$ is small, the problem is easier

If there are $k$ vertices in the optimum cover, we can find it via brute force. Try all $\binom{n}{k} \approx n^k$ sets of vertices, and see if it works, so runtime is
$$O(mn^k)$$
for a brute force algorithm.

But can we get a better dependence on $k$? It turns out yes, using the **bounded search tree method**.
- Take an edge. At least one endpoint is in the optimum vertex cover
- Try both. For each, recurse to find the rest
- This is a depth $k$ search tree with degree 2, so it takes $2^k$ steps to terminate the search
- Every step takes $O(m)$ time to check, so $O(m \cdot 2^k)$.

**Kernel method**
- In polynomial time **independent of $k$**, find a small, difficult kernel of the problem and then solve it by brute force
- If optimal vertex has size $k$, any vertex of degree $>k$ must be in OPT
- This reduces us to a graph of degree at most $k$
- There is still an optimal vertex cover of size at most $k$, and each vertex covers at most $k$ edges...so we only have $k^2$ edges in the graph.
So the time used here is really $O(m + 2^k \cdot k^2)$.
