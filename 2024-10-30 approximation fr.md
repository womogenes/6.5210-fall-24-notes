Scheduling $n$ jobs on $m$ machines to minimize the completion time

General case:
- $m$ machines
	- 1 machine?
	- P: $m$ identical machines?
	- Q: $m$ machines of different speeds?
	- R: $m$ unrelated machines? processing times as $p_{ij}$ for job $j$ on machine $i$
	- F: flow shop: each machine requires a specific amount of work on specific machines **in order**
	- O: open shop: work involves many machines but order doesn't matter
- Objective functions
	- maximum completion time $\max c_j$
	- average completion time
	- sum of $u_j$, indicator variables for a job being "late" — implies deadlines
	- sum of weighted $u_j$
	- sum of weighted $c_j$
	- $\sum F_j$ — time in system
- Constraints
	- $p_j = 1$
	- $r_j$ — jobs have release dates, and we must process a job after its release date
	- pmtn: jobs can be pre-empted (split up processing time)
	- presc: precedence constraints on the jobs

Notation: machine/constraints/objective

Last time, we showed that greedy solves the $m$-machine no-constraint max-completion-time problem within an approximation factor of 2
## Question
What is the best achievable approximation factor?
- It can't be 1, otherwise we'd have an algorithm for an NP-complete problem
- Some problems have a lower bounding constant
- For some problems, there is some value below which you cannot approximate (if P $\neq$ NP)
	- APX-hard
	- If you could approximate this problem within better than a certain value, then you could solve NP-hard problems and P = N
	- Examples
		- Set cover you can't do better than $\log n$
		- Cliques has a bad approximation ratio
- For other problems, we can get arbitrarily good approximations
	- **Definition.** An **approximation scheme** for a given problem is a collection of algorithms $A_\varepsilon$ such that each $A_\varepsilon$ is a polynomial-time $(1+\varepsilon)$-approximation algorithm
		- e.g. it might run in $n^{O\left(\frac{10000}{\varepsilon}\right)}$ time
		- A **fully polynomial approximation scheme** is polynomial in the input size AND in $1/\varepsilon$
## Knapsack
- Item sizes $s_i$ and profits $p_i$
- Knapsack size $K$
- Find largest profit that fits in the sack.

Even though we can't get an exact algorithm, we can get arbitrarily close.
- Simplify: assume we have small integer profits.
- Use dynamic programming!
- Larger profits can be assembled from smaller profits
- Define $B(j, p)$ to be the smallest set of items amongst $1\dots j$ achieving profit $\ge p$
	- Can define transitions
	- $B(j, p) = \min( B(j, p), B(j, p - p_{j+1}) + s_{j+1} )$
	- Terminate when $B(n, p) > K$ because at that point we know $p$ is unachievable

This does NOT prove that P = NP because it only works for small integer values.
- Input could contain numbers of exponential value
- What if numbers are large or not integers?
- Use **rounding**/**scaling**/etc.
- Suppose OPT = P
- Imagine scaling all profits. Make $p_i \rightarrow \lfloor (\frac{n}{\varepsilon P}) p_i \rfloor$.
- What happens to the optimum?
	- Gets scaled down by a factor of $n/(\varepsilon P)$, so we multiply $P$ by it and get new profit is $n/\varepsilon$ 
	- We lose 1 profit through each floor, so we shave off $n$
	- New optimum is $n/\varepsilon - n$, which isn't so bad because $\varepsilon$ is huge so shaving off $n$ doesn't matter much.
	- We can find a solution of this value, $n/\varepsilon - n$ (not the optimal value) in time $n^2/\varepsilon$
	- Unscaling gives
	  $$ \left(\frac{n}{\varepsilon} - n\right) \cdot \frac{\varepsilon P}{n} = P(1-\varepsilon) $$
	  - We need to know $P$ though, otherwise we can't scale
		  - If we scale by too large a $P$, then bad stuff happens because the $-n$ is no longer valid
		  - If we use a lower bound on $P$, then we are good ??
		  - To get a lower bound, we can take the biggest item that fits in the knapsack
		  - $\max p_i \ge P/n$ because pigeonhole ??
		  - Runtime is worse $\rightarrow n^3/\varepsilon$
	  - How to improve:
		  - Start with one value of $P'$, and continually make it tighter
		  - We get $O\left(\frac{n^2}{\varepsilon}\right)$ runtime
	  - Suppose $P'$ is too small, i.e. $P' \le P/2$
		  - Scaling takes OPT to at least $P \left(\frac{n}{\varepsilon P'}\right) - n$
		  - If $P' > P$, we take OPT to $\le 2n$
## Knapsack
Knapsack is only **weakly** NP-hard because it only becomes NP-hard when the numbers are large

Suggests:
- In order to use the rounding technique, we need a pseudo-polynomial algorithm to start with
- And it's only the weakly NP-hard problems that can have pseudo-polynomial algorithms
Converse:
- We know that pseudo-poly problem $\implies$ exists fully polynomial approximation scheme
- Turns out that exists fully polynomial approximation scheme $\implies$ pseudo-poly problem
	- *Proof.*
	- Suppose numbers are bounded by $T$
	- If objective is a sum of those numbers, it is bounded by $nT$ where $n$ is the number of items in the problem
	- Find a $(1+\varepsilon)$-approximation with $\varepsilon = \frac{1}{1 + nT}$ which gets us to within 1 of the answer
	- Runtime: polynomial in $nT$, i.e. we have a pseudo-polynomial algorithm
## Enumeration
"Fancy name for brute force"

Go back to $P \mid\mid c_{\text{max}}$
- Imagine we schedule the $k$ largest jobs optimally by brute force
- Then greedily schedule the remaining jobs. The idea is that the remaining jobs are small, so they don't contribute much to the slackness bound in our solution
- **Claim.** $A(I) \le \text{OPT} + p_{k+1}$ because we slack by at most $p_{k+1}$
	- Consider job that finishes last.
	- If it was in the $k$ longest jobs, the time to complete all jobs = time to complete $k$ largest jobs
		- [**WHY IS THIS TRUE**]
	- If not, .......

This gives a polynomial approximation scheme for constant $m$.

On Friday: a cleverer enumeration scheme that is polynomial no matter how big $m$ is.