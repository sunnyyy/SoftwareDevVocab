# Trees

## Non-binary tree
- node has some data
- node has `node[]` of children

## Binary tree
- node left
- node right
- data value
- WARNINGS:
  - not always binary search tree!
  - not always balanced!

## Binary search tree
- special properties:
  - nodes can be printed in order in `O(n)`
  - lookup is `O(log(n))` ___if balanced___, `O(n)` worst case
    - ditto for deletion
    - ditto for insertion
  - given a node, you can find the next-highest node in `O(log(n))`
- WARNINGS:
  - not always balanced!

## Heap
- max heap = all children of a node are smaller than itself
- min heap = all children of a node are larger than itself
- use when:
  - extracting min / max quickly are important
- if no ordering, time to find any node is `O(n)`

-----
# Algorithms for trees

## Breadth-first search (BFS)
- recursion uses extra memory bc you have to keep track of all children for current level
- recursion can be replaced with queue of children, but this still takes up memory
- use when:
  - you think your node will be among upper levels / within close distance

## Depth-first search (DFS)
- use when:
  - you think your node is toward bottom
  - e.g. when searching a job hierarchy tree for an employee who started < 3 months ago, you can assume a higher % of lower-level employees started recently
  - uses less memory (?)
  - uses recursion (?)

## Traversal
- just like search, but visit every node, usually to perform some operation on each node
- types:
  - pre-order = node, left, right
  - in-order = left, node, right
  - post-order = left, right, node
- also works for non-binary trees if you have a Comparator
- recursion is easiest for depth-first (?)

-----

# Problems

## Write a function to calculate the height of an arbitrary binary tree
```java
public static int height(Node n) {
	if (n == null) return 0;
	return 1 + Math.max(height(n.left), height(n.right));
}
```
runtime: `O(n)` because each child of each child of each child ... is checked, so each node in tree is visited 1x

## Traversals

### Recursive solution
```java
void preorder(Node n) {
	if (n == null) return;
	System.out.println(n);
	preorder(n.left);
	preorder(n.right);
}

void inorder(Node n) {
	if (n == null) return;
	preorder(n.left);
	System.out.println(n);
	preorder(n.right);
}

void postorder(Node n) {
	if (n == null) return;
	preorder(n.left);
	preorder(n.right);
	System.out.println(n);
}
```

### Iterative solution
```java
void iterativePreorder(Node n) {
	Stack<Node> stack = new Stack<>();
	stack.push(n);
	while (!stack.empty()) {
		Node.current = stack.pop();
		System.out.println(current);
		Node r = current.right;			// right goes first, bc it'll be bottom of the stack
		if (r != null) stack.push(r);
	}
	UNFINISHED
}
```

-----

## Lowest common ancestor
- given value of 2 nodes in a BST, find their lowest common ancestor
- solution 1
  - make 2 lists of ancestors & go upwards to find a common one
  - use HashSet to make comparison
  - `O(log(n)^2)` in a balanced tree, and you are on two equally-long branches with lowest ancestor at root --> worse than `O(log(n))` but better than `O(n)`
- solution 2:
  - root is ancestor to everything, so start there
  - if target values are both < current node, then current = current.left
  - if target values are both ≥ current node, then current = current.right
  - the first node you find with a value between both target values is the answer
  `O(log(n))` because at most, you only have to go down one branch

```java
Node lowestCommonAncestor(Node root, Node n1, Node n2) {
	if (n1 == null || n2 == null) throw Exception;

	while (root != null) {
		int current = root.getValue();
		if (current > n1.getValue() && current > n2.getValue()) {
			root = root.getLeft();
		} else if (current < n1.getValue && current < n2.getValue()) {
			root = root.getRight();
		}
		return root;	// value is between n1 & n2
	}
	return null;		// if empty
}
```

## Transform a tree into a heap via an array
- Q: You're given a set of ints in an unordered tree. Use an array sorting routine to transform the tree into a heap that uses a balanced binary tree as underlyihng data structure.
- Ask: Can I heapify?
  - creating heap from tree would take only `O(n)`
  - array sort alone takes at least `O(n*log(n))`
- If you must convert to array ...
  - `O(n)` to (traverse and) find the size of the tree, so that you can allocate the static array
  - `O(n)` to traverse the tree and put elements into the array
  - at least `O(n*log(n))` to sort the array, either via `Arrays.sort()` or via your own implementation
  - `O(n)` to traverse array and create heap
    - (unspecified, so let's say a min heap)
      - since each value of node is ≤ the value of its children, children of this heap will come from elements toward the end of the array
      - since you need a balanced heap, you can't just do `a[i].left = a[i+1]` for every element
      - __solution: traverse array & add children at `2i+1` and `2i+2`__
      - run through a small array manually to verify:
        - node @ i=0 has children @ i=1 & i=2
        - node @ i=1 has children @ i=3 & i=4
        - node @ i=2 has children @ i=5 & i=6
        - --> all children are larger than its parent = correct min heap

## Balance a BST
```java
// how to find new midpoint?

public Node rotateRight(Node root) {		// right node of new root
	Node newRoot = root.left;				// becomes left node of new root
	root.left = newRoot.left;				// O(1) due to in-place swap
	newRoot.right = root;
	return newRoot;
}
```

