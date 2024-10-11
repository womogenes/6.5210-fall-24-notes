# Splay trees, cont.
## One way to do inserts/deletes
### Inserts
- To insert a node, insert as normal with BST and then splay the node
- If we don't splay after insertion, this is bad b/c adversary could do a bunch of insert operations to create a chain which would be $O(n^2)$
- The splay operation is paid for by the potential function. However we must consider the potential change caused by attaching the new node
	- Turns out this is fine! Inserting as normal works
### Deletes
- If it's a leaf, delete a node as normal, then splay the parent
	- Non-leaves are trickier
- The splay of the parent pays for the cost of getting to the parent
- Also, the potential only decreases because all weights are nonnegative so we're good
### A more natural way to do inserts/deletes
### Split
split(T, x) of tree T and value x outputs two trees: T_1 and T_2
- T_1 consists of all nodes less than or equal to x
- T_2 consists of all nodes greater than or equal to x

**Implementation:** splay x and cut off the right subtree. This is O(1) amortized time b/c the splay pays for itself.
- Analysis: cutting of the subtree only decreases the potential
### Merge
merge(T_1, T_2) of two trees T_1 and T_2 with all of T_1 < all of T_2 outputs a tree T which contains all the nodes 

**Implementation:** splay the maximum value of T_1 so it will have no right subtree. Then we attach T_2 as the right subtree. This is O(1) b/c the splay pays for itself.
- Analysis: only one node (the new root of T_1) gains rank, and in particular gains $\log n$ rank [**WHY??**] . It adds $\log n$ time to the amortized cost.
### New insert
To insert(x, T) a node x into T, split T at x, then do merge(T_1, merge(x, T_2))
### New delete
Splay x, detach the left and right subtrees, then merge those subtrees.

## Drawbacks of splay trees
- In some applications, you don't want amortized cost (e.g. video games, want more-or-less online operations)
- They change on reads (and they _have_ to) so reads necessitate writes. This is bad for cache stuff (?)
- If we want to make splay trees persistent, very splay requires $\sim\log n$ writes, which uses up space and thus is bad for memory usage

There are some heuristics that can be applied to fix some of this:
1. Only splay on long searches
2. Stop splaying after "a while"
3. Splay trees can do compression
	1. Take the alphabet, put it in a splay tree. Every time you want to send a letter, go down the splay tree to find it. The path to this node is a bunch of 0s and 1s, and you can send those. This is good b/c according to the static optimality theorem, more popularly used nodes are closer to the top and thus have shorter paths --> fewer bits
### Dynamic optimality conjecture
- No one has been able to 
- "Splay trees are just so beautiful that this conjecture **must** be true"

Erik Demaine: "Tango trees" are within $\log \log n$ of the dynamic optimum
Danny Sleator: "Tango-splay variant" which still looks like a splay tree but includes extra information. It matches the $\log \log n$ closeness-factor of Tango trees

# Using integers
i.e. "things with a small number of bits"
- Fact: using integers gives you indirect addressing into arrays

## Shortest path
Dijkstra's algorithm for Fibonacci heap works in $O(m + n \log n)$ time. This is apparently optimal for arbitrary graphs.

But suppose that instead we have all edge lengths $\le c$ for some nonnegative integer $c$. 
- Suppose $c=1$; then we can use BFS, which works in $O(m)$ time
- If we have $c=2$, then pretend each length-2 edge is two edges. in general, we can split edges and do BFS in $O(cm)$ time
### Dial's algorithm
- if we run Dijkstra's, there are at most $c$ unique values in the priority queue (b/c you don't insert a node of cost $k + c$ until you delete a min node of cost $k$)
- Monotonic: the min of the pq is non-decreasing

Dial's algorithm
- Keep an array of **buckets**. Start at the root. Then look at its neighbors, and add their costs to the appropriate bucket.
- To look at the next small element, we can walk down the array until we find a nonempty bucket and take a node from there.
This is basically a priority queue with following operations:
- insert node of given cost: go to bucket `cost`, add node to the bucket
- delete-min: walk forward until we find a nonempty bucket

$m$ inserts, $O(1)$ time per insert.
$n$ delete-mins (b/c we pull each node out at most once), $O(c)$ time per delete-min

The delete-mins can be optimized due to the monotone property: next smallest element will never decrease in cost.
 - So we really just need a pointer that goes through the entire array once, which is $O(cn)$ (that's the max length possible).
Total time is therefore $O(m + cn)$, which is substantially better than $O(mc)$.

**Concern:** In modern computers, it is often space that matters more than time. i.e. reading memory is fast, but memory is a limited resource
- To fix this, recall that the elements in the array are always within an interval of length $c$. So we can instead keep an array of size $c$ and put an element of node $d$ into $d \pmod{c+1}$.
### Optimizing Dial's algorithm: 2-level buckets
- We spend most of our time traversing the empty spots in an array. How do we address this?
- 2-level buckets—have a second-level DS that tells us whether a region of the array is empty.
	- During delete-min, while we're walking the array, we can ask the DS if a region of the array is empty and if so then jump to the next region

**Implementation**
- Make blocks of $b$ buckets. There are $nc$ buckets, so the summary layer is of size $nc/b$.
	- Within each summary, keep a count of how many nodes there are in the block.
- **insert:** add it to the bucket, increase summary count — $O(1)$
- **decrease-key**: move buckets, update summary $O(1)$
- **delete-min:** scan the summary structure for first nonempty block in $O(nc/b)$ time (actually this is a one-time cost, using the monotone property), then do a $O(b)$ walk

We are doing $n$ delete-mins, so $O(nc/b) + n \cdot O(b)$. To minimize this, pick $b = \sqrt c$ because that makes $nc/b = nb$. That's not bad at all — we're essentially going from $2^{32}$ to $2^{16}$ if $c = 2^{32}$, for instance.
### Can we do better?
Add a third level of **super-blocks** !!
This improves runtime to $O(m + nc^{1/3})$.
### Can we do better?
- In general, we can keep doing this until the number of layers becomes significant.
- Assume we do $k$ levels of summarization. Each level includes $\Delta$ meta-blocks. Essentially we can imagine this as a base-$\Delta$ representation that traces a path through our tree (trie).
- There are $k$ digits, so we want $\Delta^k = c \implies \Delta = c^{1/k}$.
### Time complexity
- **insert:** $O(k)$ to walk down the trie (and update each level)
	- **decrease-key**: also $O(k)$
- **delete-min:** $O(k \cdot \Delta)$ because we have to go up at most $k$ meta-layers until we hit a nonempty node, then go down while walking, which takes $\Delta$ per walk.

Overall cost: $O(km)$ for decrease-key, $O(k \cdot c^{1/k} \cdot n)$ for delete-min, so overall $O(km + kc^{1/k} n)$. How to balance this? Solve
$$km = kc^{1/k}n \implies c = (m/n)^k \implies k = \log_{m/n} c,$$
which makes the overall runtime $O(m \log_{m/n} c)$. In general, $m$ is much larger than $n$, which makes this quantity quite small.
### Space complexity
Denardo & Fox, 1979
- How do we make the trie more lazy?
- Idea: don't push items down the trie until it's necessary for correctness
	- We know there's always stuff going on at the minimum element
	- Basically, lazy-create the trie. **Only maintain blocks that are on the path to the current minimum**. Because recall that the min pointer advances sequentially through the array! So this is actually not that hard.
	- Also, for inserts, **elements get stopped when they get off the current path to the min**.
	- For decrease-key, it's kinda like insert key but the node can only move down the trie.
	- For **delete-min**, start at the lowest level of the trie and jump up the trie while it's empty. Then scan down, and we might need to materialize.

Analysis
- Materializations are free; we can charge them to the inserts! So while it's technically $O(k + \Delta)$ for delete-min, the $k$ is paid for by the insert of the element; thus $O(\Delta)$ for delete-min.
- For $m$ inserts and $n$ delete-mins, we get  $$O(m + n(k + c^{1/k})) = O\left(m + \frac{n \log c}{\log \log c}\right). $$
  If $c = 2^{32}$, this comes out to about $64n$ which is pretty linear even while numbers are huge.
### Applications
Silverstein et. al. invented smth called "hot queues," which keeps track of the first nonempty value. This improved the time to $O(m + n(\log c)^{1/3})$.