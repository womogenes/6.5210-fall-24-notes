- The idea is: we want a hash function that generates no collisions.
## Idea 1
- Suppose we want to hash $n$ items. Then allocate $s$ possible hashes, and the total expected number of collisions is $n^2/(2s)$. If we make $s = n^2$, then the expected number of collisions is 1/2.
- Due to **Markov's inequality**, there must be a 1/2 chance of getting zero collisions.
	- Markov's inequality:
	  $$\mathrm{P}(X \ge a) \le \frac{\mathrm{E}(x)}{a}. $$
- Try this algorithm multiple times until it works.
	- This is *probably* fast and is known as a **Las Vegas algorithm**.

**Improvement:** Can we make $s$ smaller?
- Not with this algorithm — you can't do better than $s = \sqrt n$ because of the birthday paradox.

[**QUESTION:**] How do you quickly check for hashes? Wouldn't you have to compute the hashes on all $n$ items first?

## Ajtai, Komlos, and Szemeredi
"Two level perfect hashing"
- Hash the $n$ items to $O(n)$ buckets using 2-universal hash family.
- Build a 2-universal **perfect** hash table in quadratic space on each bucket.
- Turns out, due to math, this works out to $O(n)$ space and $O(1)$ time.