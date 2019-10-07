# Linked Lists

## Basic facts

-----

# Problems

## Find `k`th-to-last element in a singly LL
- constraint: `k ≤ n`
- solution of `O(2n) = O(n)`
  - go through list 2x: first to find length, then to find kth-to-last element
  - storage size: 1 node, for a copy of the pointer to head
- solution of `O(1n) = O(n)`
  - create copy of `k` nodes, either as LL or queue
  - first `1 .. k` nodes are saved to that copy
  - as you go through each node in the list (just once), remove oldest node from head of _copy_ and add new node into tail of _copy_
  - when you reach the end, return the first element of _copy_, which will be `k` nodes away from the end of the list, and therefore the `k`th-to-last element
  - storage size: data structure of size `k`
  - good for: reading from disk, if `k` isn't too large

## Find if a singly LL contains a cycle
- solution with `O(n)`
  - slow pointer (i = i+1) starts at first node
  - fast pointer (j = j+2) starts at second node
  - each iteration of the loop updates both nodes at the same time
  - if fast pointer hits a null, then LL is acyclic
    - this would take approx. `O(n/2) = O(n)`
  - if fast pointer and slow pointer end up on the same element, it's because the fast pointer went around the cycle, meaning there _is_ a cycle
    - this would take exactly `n` iterations, because the slow and fast pointers would both end up on the last node
      - confused? test on paper 1 cycle of odd length & 1 cycle of even length
  - take max big-O of both cases --> since both cases are `O(n)`, overall solution is `O(n)`

## Flatten a doubly LL (assuming no cycles)
- goal: all nodes appear in a single-level, doubly LL
- solution 1:
  - tree traversal via recursion with each node examined 1x
  - some overhead for recursion
  - extra data structure to store flattened list, which would be of size n
  - __cons:__ copied list is inefficient for large structures
- __solution 2:__
  - traverse first level from start
  - every time you encounter a node with a child, append the child & its list to the end of the first level
  - continue traversing the first level, and (at the tail of the original first level) you will get to the start of the old second level
  - if you keep doing this, eventually, every child list will be appended
  - efficiency:
    - every node after first level is examined 2x
      - 1x for updating tail pointer
      - 1x to see if it has a child
    - `O(2n) = O(n)`
    - more efficient bc no recursion or extra memory involved
- solution 3:
  - same as solution 2 but insert child node immediately after parent node --> made possible since the problem involves a doubly LL

## Unflatten a doubly LL (from the previous problem)
- with the previous flattened list of yours, assume the children were never deleted from each instance variable
- assuming solutions 2 or 3, traverse start to finish
- every time you find a node with a child,
  - separate the child from its previous node
  - start traversing that new child list
  - then continue traversing the original child list
- pseudocode:
```
while not at the end
	if current node has a child
		separate the child from its previous node
		explore, starting with the child
	go onto next node
```

