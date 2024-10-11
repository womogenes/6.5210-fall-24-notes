- Denardo and Fox: trie data structure for **monotone** priority queue application
	- Summary: trie data structure that saves memory by only initializing one layer of the trie at a time
	- On inserts, elements propagate as far as they can go and then stop when they want to go into a layer that hasn't been materialized yet (lazy lading)
	- On decrease-key, elements only move down b/c they go into an earlier bucket (closer to the min)

> **NOTE:** Andrew Goldberg has good papers on implementing algorithms + seeing how well they do in practice

## van Emde Boas priority queue
- Idea: recursively uses optimal data structure to execute operations within optimal data structure

- Setup: Take an array of buckets with a summary structure on top and a recursive priority queue for the summary.
- A vEB queue on $b$-bit numbers is a structure which contains:
	- `Q.min`, a box for the actual minimum value (not a pointer to it, the actual value)
	- `Q.low`, an array for queues
		- Break the $b$ bits into $b/2$ "high" bits and $b/2$ "low" bits
		- `Q.low`
		- `Q.high` is a queue tracking which `Q.low` arrays are empty
			- It's a queue on $2^{b/2}$ possible high bit values.
		- Imagine universe as $\sqrt{U} \times \sqrt{U}$ grid, with `Q.low[]` containing the rows, and `Q.high` tracking which rows are nonempty.
### Inserts
To insert $x$: If $x$ < `Q.min`, swap out `Q.min` for $x$.
- Expand $x$ as $(x_h, x_\ell)$.
- Look into `Q.low[x_h]`. If it's nonempty, insert $x_\ell$ into it.
	- This is $c + T(b/2)$.
- Otherwise, create a queue at `Q.low[x_h]` with $x_\ell$ as its minimum and insert this new queue into `Q.high`.
	- This is $c + T(b/2)$.

Runtime $T(b)$ is associated with a lookup (constant) and insert, which is $T(b/2)$. This comes out to $T(b) = O(\log b) = \log \log U$.
### Delete-min
- Remove Q.min, then replace
- To replace, get `Q.high.min` and look into bucket `Q.low[Q.high.min]` for the min. Run delete-min on it, then if we've emptied this `Q.low[...]`, also delete-min from `Q.high` (because the `Q.low[...]` queue is now empty).
	- If `Q.high.min` is null, then `Q.high` is empty, so $Q$ becomes empty.
	- **Note:** If `Q.high` becomes empty, our queue is _not_ empty, because `Q.min` is a separate element.

Runtime is also $T(b) = 1 + T(b/2)$, which is $\log \log U$.
### Limitations
- If we have huge $U$ but small $n$, Thorup '96 came up with an idea to improve on this:
	- You can make this $O(\log \log n)$ (where $n$ is the number of items in the priority queue) as opposed to $O(\log \log U)$ (where $U$ is the largest number)

- Space: this uses $O(\sqrt U$) space which is possibly bigger than how much memory is available on the machine
- Solution: **hash tables!**
## Hashing
We have $n$ items in a range $1 \dots m$. We are given that $m$ fits in a machine word — $\log m$ bits
Goal: We want to have really fast lookups.
Hashing: smaller array, store key $k$ in bucket $h(k)$ for some hash for some $h: \{1\dots m\} \rightarrow \{1\dots s\}$ where $s$ is the size.

Problem: **collisions**. When two items are inserted that have the same hash, they get put in the same bucket. To fix this, we can put linked lists in buckets
- But worst case, if we have lots of collisions, there are many items in a single linked list/bucket, which makes inserts slow.

Goal: find a hash function that has very few collisions.
### Fix
- Be **instance-dependent**; that is, define a "hash family" which is a set of hash functions such that at least one is good for any input set of items.
	- We need a small set of hash functions that can be evaluated quickly
### Randomization
- Use a "random hash function"
	- First  consider a literally random hash function (which, it should be noted, would take $m$ space...) what kind of collision behavior would we get out?
	- Imagine we have $m$ items and they get mapped randomly to a size $s$ array
	- If $n = s$, the max number of items in a bucket is $O\left(\frac{\log n}{\log \log n}\right)$ with very high probability.
	- Expected behavior for an arbitrary item that gets thrown into the hash table:
		- Use indicator variables. Write $c_{ij}$ be $1$ if the $j$th item lands in the $i$th bucket. This variable is $1$ with probability $1/s$ and $0$ with probability $1 - \frac1s$.
		- Total number of collisions between our item and item $i$ is $\sum_{j} c_{ij}$ where $j$ goes from $1$ to $n-1$. The expectation of this value can be computed with **linearity of expectation**! Turns out this thing is $\frac{n-1}{s}$.
		- In particular, if $n \le s$, then this value is less than 1. So if the hash table is at least as big as we need it to be ($n$), we get very few collisions.

The issue is, of course, that writing down a random hash function takes $O(m)$ space (which could be huge, if $m=2^{64}$ for instance)
### Universal hashing
- All we need to ensure is that hashes are *pairwise independent* i.e. the value of $h(x)$ is uniformly independent on value of any other $h(y)$.
- Pick a prime number $p$ in the range $m\dots 2m$. Pick two numbers $a, b$ in this range (or in $0\dots m -1$ works too). Define a function $h_{ab} (x)$ to be $(ax + b) \pmod{p}$ taken mod $s$ (cause $p$ is huge).
- Claim: if $a, b$ are chosen *at random*, then $(ax + b) \pmod{p}$ and $(ay + b) \pmod{p}$ are uniform & pairwise independent.

**Proof of pairwise independence.** Fix $s$ and $t$, then let $x$ and $y$ be random values that we're hashing; the probability that hashing $x$ and $y$ produces $ax + b = s$ and $ay + b = t$ is $(1/p)^2$ (proof of this requires invertibility of the $p$-field).

