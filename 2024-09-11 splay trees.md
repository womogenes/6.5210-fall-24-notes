Splay trees ("self-adjusting binary search trees") were developed by Sleator and Tarjan in 1985
- Goal: Binary Search Trees — insert, delete, search. We'll begin with search and move to insert/delete at the end.

## Motivation
- Often, we try to keep the tree balanced using auxiliary information (red-black trees, marked bits, etc.)
- If we want to be _truly_ lazy, we should do nothing until a search happens
- We keep **no extra information** in the tree
## Intuition
Question: What goes wrong if we **don't** try to keep a tree in balance?
- Searches take a long time because the node we're looking for is too far down the tree
- To fix this, maybe we should move this item up using rotations
- An adversary would keep querying for nodes at the bottom of the tree. So we would like to take long paths and make them short (cutting it in half, for instance, would be nice).

"Out of balance"
- One way to define a bad node is one that has a vast majority of its descendants on one side
- In particular, if we have a *large subtree* deep in our tree, we want to raise it higher to the *height where it belongs given its size*.
## Implementation
- Single rotations won't work because consider a long chain.
- We will use **double rotations** cut long chains in half
- Three operations:
	- Zig-zig rotation: ![[MIT/6.5210/attachments/dzhang-splaystep-rr.png]]
	- Zig-zag rotation: ![[dzhang-splaystep-lr.png]]
	- Zig rotation: ![[dzhang-splaystep-r 1.png]]

Operation **splay(x)**: use double rotations to move **x** towards the root. Possibly one single rotation, if the path length is odd.
### Search
- Find **x** the usual way, then splay it to the root.
## Analysis
Since we aren't keeping extra info about the tree, the potential function must be a function of **only** the shape of the tree

**Assumption**: each node has a weight $w_x \ge 0$.
- Define the **size** of $x$ to be the sum over all descendants of $w_y$, including $x$ itself.
- Define the **rank** of $x$ to be log base 2 of the size of $x$. This is kinda the "height" that $x$ should have if it were perfectly balanced.
- Define our potential function to be sum over all $x$ of their **ranks**.
	- This is nice because a maximally unbalanced tree has lots of nodes with high rank; whereas a balanced tree has lots of nodes with low rank, some nodes with medium rank, and very few nodes with high rank.

### Access lemma
**Access lemma:** the amortized time to splay a node $x$ to the root $t$ is at most
$$3(r(t) - r(x)) + 1. $$
Observe that $r(t)$ is a constant $\log n$. So the amortized time to splay a node is how deep it **should** be in the tree.

*Proof.* Suppose all $w_x$ equals 1. Then $r(t) = \log n$. We want splay time and search time to come out to $O(\log n$).
Note: To make this bound true for unsuccessful search, we must splay the last node visited. This is true because if we spent a lot of time looking for a node, we should still decrease the potential to pay for the time we spent.
- We will prove this one double rotation at a time and telescope
- We focus on the zig-zig case.

Let $r$ be the rank function before the rotation, and let $r'$ be the rank function after the rotation.
- The actual cost is 2 (because 2 rotations)
- The changes in potential happen only to $x$, $y$, and $z$. Subtrees are unaffected, and also ancestors are unaffected. Thus,
  $$ \Delta \Phi = (r'(x) + r'(y) + r'(z)) - (r(x) + r(y) + r(z)). $$
- We want to show:
	- The overall amortized cost $2 + \Delta\Phi \le 3(r'(x) - r'(x))$ because this will eventually telescope.
	- The question is: How do we pay for the $2$?
- **INTUITION:** How to pay for the $2$
	- If $A$, $B$, $C$, and $D$ are all empty, the potential function doesn't change.
	- If they are not all empty, the question is whether the weight is more in $A$ and $B$ or whether it's more in $C$ and $D$.
	- If $x$'s rank increases a lot (i.e. $r'(x) - r(x)$ is big), 3 times the change of rank is a lot bigger than 1 times the change in rank. This probably means $C$ and $D$ are big.
	- If, on the other hand, $x$'s rank is *unchanged*, $C$ and $D$ are probably really small.
		- In this case, we claim the overall potential has decreased because $z$ loses all its potential. This pays for the cost $2$ of the double rotation.
### Formal analysis
Amortized cost of a zig zig rotation is
$$ 2 + \Delta\Phi = 2 + r'(x) + r'(y) + r'(z) - r(x) - r(y) - r(z). $$
We can cancel $r'(x)$ with $r(z)$ because old $z$ has the same subtree as new $x$. So we simplify to
$$ 2 + \Delta\Phi = 2 + r'(y) + r'(z) - r(x) - r(y). $$
Now observe that $r(y) \ge r(x)$ because $y$ is an ancestor of $x$. Also, $y$ is a descendant of $z$, so $r(z) \ge r(y)$. We take $y$ out of the picture to get
$$2 + \Delta\Phi \le 2 + r'(x) + r'(z) - r(x) - r(x). $$
We gather terms to get
$$ = 2 + (r'(z) - r(x)) + (r'(x) - r(x)). $$
This is good because $r'(x) - r(x)$ appears in the result we want. It is now sufficient to show that
$$ 2 + r'(z) - r(x) \le 2(r'(x) - r(x)) $$
because we want the $(r'(z) - r(x))$ term to be at least $2(r'(x) - r(x))$. Equivalently,
$$ r'(z) + r(x) - 2r'(x) \le -2. $$
Now put $s$ back into the equation: 
$$ \log \frac{s'(z)}{s'(x)} + \log \frac{s(x)}{s'(x)} \le -2. $$
We want to show that this is true. What is $s'(x)$? It's just the size of this entire subtree. Observe that $s(x)$ contains $x, A, B$ and $s'(z)$ contains $z, C, D$. So we know that
$$ \log \frac{s'(z)}{s'(x)} + \log \frac{s(x)}{s'(x)} \le \log a + \log (1 - a) $$
if we define $a$ to be the proportion of the entire subtree that $s(x)$ takes up. Now note that the right hand side is $\log_2 a(1-a)$, which maximizes at $a=1/2$ and expression = $\log_2 1/4 = -2$.

This proves the access lemma !! woohoo
## Other stuff
The amortized cost of an operation is equal to the real cost plus the final potential minus the initial potential.

The final potential is at most $n \log n$ (sum of ranks). The initial potential is $0$.
Now generalize: suppose $W = \sum w_x$. The final potential is at most $n \log W$, and the initial potential is $\sum \log w_x$. The "correction factor" is therefore
$$ \sum \log \frac{W}{w_x}. $$
Since $w_x$ is "how high node $x$ *should* be," $w_x$ is the cost of splaying $x$ to the top. Once every node $x$ is splayed once, the amortized cost is as good for the 

If you start with an empty tree, you don't have to worry about the correction factor.
## Applications of the access lemma
- We have shown that a splay tree is better than a balanced binary search tree.
### Static optimality
- Suppose we know about the searches beforehand. We should put popular nodes towards the root.
- If we find an item, we can imagine the path to that item as a "name" for that item. Entropy is the study of how many bits we need to name items.
- If we have probabilities $p_x$ on items $x$, we expect the number of bits needed for all the items to be $p_x \log p_x$.

Let's analyze this by going back to the splay operation. Set $w_x = p_x$ for our analysis. Then $W = \sum p_x = 1$. The access lemma tells us that the amortized time to find and splay $x$ is
$$ 3(r(t) - r(x)) + 1. $$
[**REVIEW THIS**]
### Finger in a search tree
- If a lot of accesses are happening on one area of the tree, we can keep a finger in the tree.
- Let node $f$ be our "finger." Then the search time is approximately the log of the difference in rank between the finger and the target.
- Splay trees maximize this performance ... if we set the weights properly we can prove this.
### Working sets (caches)
- Splay trees can match the performance of "working sets" too ...
### Dynamic optimality
