<!--?title Randomized heap -->
#Randomized heap

Randomized heap - a heap, which through the using of a random number generator lets you perform all necessary operations in logarithmic expected time.

A **heap** is a binary tree, for every vertex is true that the value in this vertex is less than or equal to value in all its descendants (this is a min-heap; of course, you can define a heap symmetrically to the maximum). Thus, the root of heap contains the minimum value.

Following is a standard set of operations defined for the heaps:

- Adding an item
- Finding the minimum
- Removing the minimum (remove it from the tree and return its own value)
- Merging of two heaps (returns a heap containing elements of both heaps; duplicates are not removed)
- Removal of any element in heap (if position is known in the tree)

Randomized heap lets you to perform all these operations within the expected time O (log N) with a very simple implementation.

##Data structure
Randomized heap have next structure:

```
struct tree {
	T value;
	tree * l, * r;
};
```

Top of the tree contains the __value__ of some type __T__, which is also have a defined comparison operator ( __operator__  <). In addition, this structure contains pointers to the left and right sons (which are equal to 0 if no corresponding child detected).

##Operations execution

It is easy to see that all operations, described above,  are reduced to the same operation: **merging** two heaps into one. Indeed, the addition of an element to the heap is equivalent to merge this heap with a heap, consisting of a single element. Finding the minimum does not require any action - heap’s root contains element with minimum value. Removing the minimum is equivalent to 
merging of the left and right subtrees into one tree which will be replaced with a root. Finally, the removal of any element similar to a removal of minimal element: whole subtree with a root in this vertex is replaced by the result of merging of 2 subtrees, the left and right sons of this vertex.

So, we actually need to implement only the operation of merging two heaps together, all other operations are reduced to this operation.

If we have 2 heaps - __T1__ and __T2__ - we need to return their union. It is clear that the root of each of these heaps contains their own minimal value, so, resulting heap should contains the minimum from these 2 values. After comparing we will get a root element with minimal value, so here we just need to merge the sons of selected heap  (with minimal root) and other heap.
If we can somehow select one of the sons, then we can just make a union of a subtree in a root with this son and heap. Thus, we again come to the merge operation. Sooner or later, this process will stop (it will take, of course, no more than the sum of the heights of heaps).

Thus, to achieve a logarithmic asymptotic behavior in the average case, we need to specify the method of selecting one of the two sons,  so that the average length of the traveled path will take no longer than the logarithm of the number of elements in the heap. It is easy to guess that this choice we will make **randomly**, so the implementation of the merge operation is obtained as follows:

```
tree * merge (tree * t1, tree * t2) {
	if (!t1 || !t2)
		return t1 ? t1 : t2;
	if (t2->value < t1->value)
		swap (t1, t2);
	if (rand() & 1)
		swap (t1->l, t1->r);
	t1->l = merge (t1->l, t2);
	return t1;
}

```
First of all we will check if action is necessary to make - one of the heaps can be empty.
Otherwise, we will swap a __T1__ heap with __T2__ heap, if __T1__ does not contains the minimal value in the root.Finally, we believe that the second heap __T2__ will be merged with the left son of the root heap __T1__, so we randomly exchange the left and right sons, and then we merge the left son and the second heap.

##Asymptotic 
We introduce the random variable __h(T)__, indicating **the length of the random path** from root to leaf (the number of edges between them). It is clearly that the __merge__ algorithm runs in __O (h (T1) + h (T2))__ operations. Therefore, the random variable __h(T)__ must be investigated before we will start investigation of the asymptotic behavior of the algorithm.

###Mathematical expectation
It is alleged that the expectation of __h(T)__ is bounded above by the logarithm of the number __n__ of vertices in the heap:

$Eh(T) \le log(n+1)$


This can be proved easily by induction. Let __L__ and __R__ - respectively the left and right subtrees of root of __T__ heap and __nl__ and __nr__ - the numbers of vertices in them (of course, __n__ = __nl__ + __nr__ + 1).

Then we have:

$Eh(T) = 1 + \frac12 (Eh(L)+Eh(R)) \le 1 + \frac12 (log(n_l+1)+log(n_r+1)) = $
$= 1 + log\sqrt{(n_l+1)(n_r+1)} = log2 \sqrt{(n_l+1)(n_r+1)} \le$
$\le log{2((n_l+1)+(n_r+1))\over2} = log(n_l+n_r+2) = log(n+1)$

So, Q.E.D.

###Exceeding of the expected assessment
Let us show that the probability of exceeding of the expected assessment is small:

$P\{ h(T) \gt \log n (c+1)  \} \lt {1 \over n^c} $

for any positive constant c.

Let __P__ be the set of paths from the root to leaves, the length of which exceeds (c + 1) \ log n. Note that the probability to be chosen for any path __p__ with length | p | is equal to $2^{-|p|}$

Then we get:

$P\{h(T) \gt \log n(c+1) \} = \sum_{p \in P}2^{-(c+1)\log n} = |P|n^{-(c+1)} \le n^{-c}$

So, Q.E.D.

###The asymptotic behavior of the algorithm
Thus, the algorithm __merge__  is performed in O (\ log n) on average (so the same for all depended operation).

Moreover, for any positive constant $\epsilon$ exists a positive constant __c__, which is 
the likelihood that the operation would require more than $c\log n$ operations, is less than $n ^ {- \epsilon}$ (it is in some ways describes the worst behavior of the algorithm).

