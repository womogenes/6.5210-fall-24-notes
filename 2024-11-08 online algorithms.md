# Online algorithms
i.e. "building the engine while the airplane is flying"

One can kind of imagine data structures as online problems.
## Input sequence
Input sequence $\sigma$
- As each symbol arrives, we must act (output, update, etc.)
- Over the entire sequence, we pay a total cost $c_\text{algorithm}(\sigma)$
- Goal: minimize $c_\text{algorithm}(\sigma)$
- To do analysis, we need to compare our cost to the optimal algorithm for each sequence
	- We want an algorithm that does as well as possible
	- But if there's no algorithm that does well, it's ok if our algorithm performs poorly

To quantify the analysis of our algorithm: we say an algorithm $A$ is **asymptotically $k$-competitive** if, for every sequence $\sigma$,
$$c_A(\sigma) \le k \cdot c_\text{OPT}(\sigma) + O(1).$$
In the optimal case, OPT knows what $\sigma$ is, i.e. it knows the future. The constant $k$ is called the **competitive ratio** of our algorithm.
## Example: ski rental
- Periodically go skiing
- Every time, rent for $1 or buy for $T

Algorithm space
- Once you buy skis, you never have to buy them again
- i.e. rent for $d$ days, then buy
- How do we choose $d$?

Analysis
- If $d=0$, worst possible case if we buy immediately: the sequence that is "go once and never again" gives a ratio of $T$
- If $d = \infty$, worst possible case if we rent all the time: sequence that is "go forever" gives a ratio of $n/T$ if we go $n$ times, so as $n \rightarrow \infty$, we 
- Neither extreme makes sense :( is there some $d$ in the middle?

Best choice of $d$
- **Claim:** worst case is to do $d+1$ days of skiing
- If you keep skiing after you buy skis, it costs you nothing more
- Before buying skis:
	- Ratio is $1$ if the adversary is also renting. Ratio gets worse if the adversary bought their skis
	- Before we buy, the ratio does not improve
	- After we buy, our cost doesn't increase, but the adversary's can only increase. Thus the ratio does not get worse after this point.
	- So the moment of the worst ratio is the moment right after we buy skis

At the moment we purchase skis, we pay $d + T$
- The OPT pays $\min(d+1, T)$
- Competitive ratio is
  $$\frac{d + T}{\min(d+1, T)}.$$
- This is best at $d + 1 = T$, which gives
  $$\frac{2T - 1}{T} = 2 - \frac1T \le 2.$$
  So this is 2-competitive to the optimal algorithm.
## Market
We have a thing that goes up and down in price. Price fluctuates. How much should we sell when?
- We have 1 unit of boba. How much should we sell, and when?
- We do not know when the market closes.

Know max price $M$ and min price $m$. Also know final option to sell (i.e. at the end, if we haven't sold, we can sell everything at $m$)
- Algorithm: do various things at various prices
- Something we can do is just sell everything on the first day at min price $m$. Can we do better?
- Goal: set a threshold ("reserve price") $R$ when we will sell everything
	- Otherwise, we wait
- Adversarial strategy: one of these two:
	- Send to $M$
	- Send to $R - \varepsilon$
	- Setting $R = \sqrt{Mm}$ gives the best ratio, which is $\sqrt{M/m}$

Another strategy
- Put different amounts of boba in different containers, and set a different reserve price on each
- Two thresholds:
  $$[m, M^{1/3} m^{2/3}, M^{2/3} m^{1/3}, M]$$
- Set reserve prices at powers of two. Have $\log \varphi$ reserve prices at $2^i \cdot m$ (i.e. $m, 2m, 4m$, etc.).
- Sell $1/\log \varphi$ fraction at the $i$th reserve price
- We will sell $1/\log \varphi$ some for at least 1/2 max price
- Thus we get a $\log \varphi$ competitive ratio

If we have a car, we can't divide it up into pieces. But we can use **randomization** to effectively cut it up. Use a random algorithm to pick one of the above thresholds.
- In expectation, we do better than $1/\log\varphi$ of the optimum

**Note:** the adversary doesn't know your algorithm if it's randomized
## $P \mid\mid C_\text{max}$ online
Jobs keep arriving, we need to assign them to machines as they arrive, we will assign them to optimal algorithm
- Graham's rule is an online algorithm (assign to most free machine)
- Can you beat $2 - 1/m$?

Bartal, Fiat, et. al in 1995 improved the ratio to $2 - \frac{1}{70} approx 1.986$
- Intentionally underload some of the machines
Karger et. al, 1945:  $\approx 1.945$
Albers, 1997: $\approx 1.923$
## Paging
Disk drive is slow. Fast small "memory," and slow large "disk drive"
$\sigma$: sequence of data accesses

- If data's block is in memory, we call it a **hit** and the access is free
- If it's not in memory, we call it a **miss** and we have to pay 1 to fetch the block
	- We can't keep all data in memory though (it's small)
	- If memory gets full of blocks, on fetch we must **evict** a block to make room for new
	- Memory holds $k$ blocks
- Cost: # of misses

Algorithms
- LRU: evict the **least recently used** thing
- FIFO: first in first out
- Flush when full: when cache is full, toss everything and start over
- LIFO: last in first out
- Least frequently used

Analysis
- It turns out that LRU, FIFO, and flush when full are all $k$-competitive
- LIFO and least frequently used are not (i.e. you can make them do infinitely worse than the best algorithm on some sequences)

Consider $\sigma$ and break it into intervals of $k+1$ faults (misses)
- Compare to the optimal algorithm phase-by-phase
- How do we make LRU or FIFO or flush when full fail $k+1$ times?
	- If there are only $k$ pages in a subsequences, you only get $k$ faults
	- $k+1$ faults $\implies$ $k+1$ distinct page requests
	- With these requests, OPT can't keep all of them in memory, so one of them must be a fault
	- Thus we have a ratio of $(k+1)/1 \approx k$.
	- 