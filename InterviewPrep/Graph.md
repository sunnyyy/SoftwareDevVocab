# Graphs

## Quick details
- model harder real-world problems than trees
- like in trees, nodes have children
- may contain loops
- node = "vertex"
- line = "edge"
- __"directed"__ = edges point in one direction only
  - e.g. acqueducts connecting cities, assuming water only flows one way
- __"undirected"__ = edges point in both directions
  - e.g. air travel

### Representations
- adjacency list of neighbor nodes
- adjacency matrix = square matrix of `n^2` with element at `i`, `j` representing # edges from `i` to `j`

-----

# Algorithms for graphs

- BFS / DFS, but watch out for loops

## Dijkstra's Algorithm

-----

# Problems

## Six degrees of Kevin Bacon (find length of shortest path to "Kevin Bacon" node)
- consider BFS
  - how to avoid cycles?
    - change a variable to mark the node as "visited"
    - \*OR\* use a separate data structure to track visited nodes
  - recursive solution
  	- recursive base case = node with no adjacent unvisited node
  - iterative solution
    - use a queue
    - on each iterative cyclle, remove a node from the front & add each unvisited adjacent node
    - end when you find the target \*OR\* queue is empty (all searchable nodes searched)
- how to keep track of distance?
  - constantly keep track of # edges away from start --> mark each node with its Bacon #
  - Bacon # of a new unvisited node = current Bacon # + 1
    - this solution makes insertion of a new Actor node easier
  - initialize each node with an invalid Bacon # of 1

```java
public class ActorGraphNode {

	private String name;	// used as ID, but irl we should have an ID var
	private Set<ActorGraphNode> linkedActors;
	private int baconNumber = -1;

	public ActorGraphNode (String name) {
		this.name = name;
		linkedActors = new HashSet<ActorGraphNode>();
	}

	public void linkCostar(ActorGraphNode costar) {
		linkedActors.add(costar);
		costar.getLinkedActors().add(this);
	}

	public Set<ActorGraphNode> getLinkedActors() {
		return linkedActors;
	}

	public int getBaconNumber() {
		return baconNumber;
	}

	public void setBaconNumber(int bn) {
		this.baconNumber = bn;
	}

	/**
	 * only to be called by the Kevin Bacon node
	 * 
	 * runtime O(n) because each node will be visited 2x,
	 * 1x to add to queue and 1x to check its children
	 */
	public void setAllBaconNumbers() {
		ActorGraphNode current = this;
		current.setBaconNumber(0);
		Queue<ActorGraphNode> q = new Queue<>();
		q.add(current);
		while (q.peek() != null) {
			ActorGraphNode current = q.remove();
			for (ActorGraphNode n : current.getLinkedActors()) {
				// this node has not yet been visited
				if (n.getBaconNumber() == -1) {
					n.setBaconNumber( current.getBaconNumber() + 1 );
					q.add(n);
				}
			}
		}
	}
}


