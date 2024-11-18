Deterministic paging: $k$-competitive, which is tight
Last time we devised a randomized algorithm that is $O(\log k)$-competitive
- Is this tight? What's a lower bound?

**Yao's minimax principle** for lower bounds on $k$-competitive ratios
- Recognizes that a randomized algorithm is a probability distribution over deterministic algorithms
- Any random algorithm uses a series of random numbers
- Each such series defines a specific deterministic behavior for the algorithm

## Game theory — John von Neumann
Zero-sum, 2-player games
Extensive form
- List of strategies for each player (strategy is interpreted very broadly — for **every possible** state of the game, a strategy lists out the next move)
- Payoff matrix $M_{ij}$ describes the payoff for player 1 playing $i$ and player 2 playing $j$

**Mixed strategies**
- Probability distribution over pure strategies
- Vector for player 1, summing to 1, all nonnegative
- We can discuss expected payoff for the game
  $$\mathbb{E}\text{payoff} = \sum_{ij} p_i q_i M_{ij} = p M q.$$
- von Neumann: every zero-sum, two-player game has optimum equilibrium mixed strategies
	- i.e. a pair of strategies $p, q$ such that even knowing strategy $q$, we cannot generate a better $p$ for player 1. Likewise, we cannot generate a better $q$ for player 2.
	- Claim: suppose I announce $p$. Then there exists a pure strategy $q$ that is at least as good (by "there always exists something at least as good as the average")
## Yao's minimax principle
We look at randomized online algorithm design as a game.
- There is an adversary that picks input sequences
- Our strategies are possible algorithms
- Adversary has a probability distribution over input sequences
- **Theorem:** If there exists a distribution over input sequences $\sigma$ such that **no** deterministic algorithm has expected competitiveness $k$, then there is no randomized algorithm with (expected) competitiveness $k$. Formally:
  $$E_\sigma(C_A(\sigma)) \le k E_\sigma(C_\text{OPT}(\sigma))$$
  - *Proof.* Translate into a payoff matrix where payoff given $A$, $\sigma$ is $C_A(\sigma) - kC_\text{OPT}(\sigma)$
	  - If $A$ is $k$-competitive, this quantity is definitionally ${\le 0}$
  - Proof by contradiction. Assume there exists a $k$-competitive randomized algorithm
	  - That is, the expected value of our cost minus $k$ times OPT cost is $\le 0$
	  - This is true even if adversary chooses their best randomized strategy
## Paging
Want to show: there is no better randomized algorithm than the marking one
- Need to give a distribution over sequences $\sigma$ such that no deterministic algorithm, even knowing the distribution, beats our $\log k$ bound
- Involves a set of $k+1$ sequences
	- Completely random requests! So no algorithm can hope to do anything useful
- Fault probability for ANY algorithm: $\frac{1}{k+1}$, so expect $\frac{n}{k+1}$ faults
- Prophetic algorithm: when we need to evict, always evict the page that's farthest in the future
	- How many faults does this make? We look at distance between faults.
	- Suppose we've just evicted.
	- By furthest-in-future, we need to wait **at least** as long as it takes to see all $k+1$ pages
		- This takes $\Theta(k \log k)$ time by "coupon collector problem"
		- But we fault every $k+1$ steps or so
	- Prophetic algorithm faults $1/\log k$ times as often as our online algorithm does
## $k$-server problem
By Manasse et. al '88

Metric space with $k$ servers moving among points
- req point in space
- serve: move a server there
- We always finish serving before we get the next request
Paging is a special case of this problem, where we move between two points (memory and hard drive)

There is an offline solution due to min cost flow.
### Algorithms
Greedy? Not competitive
- Imagine we have three points: two close together on the left, and one far to the right
- We start with a server on one of the left points, and a server on the right point
- Imagine our requests alternate between the two left points for a long, long time

Fiat at. al: **harmonic algorithm**
- Move a server with probability proportional to 1/distance
- Reduced to $O\left(k^k\right)$ competitive ratio

2001: $2k$-competitive
- Uses a Work Function: how much would it cost to shift from current state to optimal state?
### on a line
- A request will always be served by one of the two servers on either side of the request point