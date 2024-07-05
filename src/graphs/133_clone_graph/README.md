# [133. Clone Graph](https://leetcode.com/problems/clone-graph/)
> Return a deep copy of a connected undirected graph.
### Input
* `adjList`: A list containing adjacent nodes of each node.
	* The number of nodes in the graph is in the range `[0, 100]`.
	* `1 <= Node.val <= 100`
	* `Node.val` is unique for each node.
	* There are no repeated edges and no self-loops in the graph.
	* The Graph is connected and all nodes can be visited starting from the given node.
	* The given node will always be the first node with `val = 1`.
### Output
The deep copy of the graph.
### Example
```
Input: adjList = [[2, 4], [1, 3], [2, 4], [1, 3]]
Output: [[2, 4], [1, 3], [2, 4], [1, 3]]
Explanation: There are 4 nodes in the graph.
1st node (val = 1)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
2nd node (val = 2)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
3rd node (val = 3)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
4th node (val = 4)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
```
### Idea
To clone a graph, we must traverse it by either DFS or BFS. Then, for each visited node, we run the following process,
1. Clone the node.
2. **Outlink** to all the cloned neighbors.

What's important to mention is that we must not clone duplicated nodes for a single node in the original graph. Hence, we'll maintain a hash map mapping from the old node to its cloned counterpart to avoid duplicates.
#### 1. DFS
DFS goes as deep as possible then returns back. For each currently visited node, we complete the **outlinks** to all its neighbors.
```python
def cloneGraph(node: Optional['Node']) -> Optional['Node']:
    def _dfs(node: Node) -> Node:
        if node in o2n:
            return o2n[node]

        clone = Node(node.val)
        o2n[node] = clone
        for nei in node.neighbors:
            clone_nei = _dfs(nei)
            clone.neighbors.append(clone_nei)

        return clone

    o2n = {}
    return None if node is None else _dfs(node)
```
* Time complexity: $O(|V| + |E|)$, where $|V|$ is the number of nodes and $|E|$ is the number of edges.
	* Traversing a graph means visiting all the nodes are steps through all the edges, which takes $O(|V| + |E|)$ to complete.
* Space complexity: $O(|V| + |E|)$
	* The input and output graph take $O(|V| + |E|)$.
	* The depth of call stack takes $O(|V| + |E|)$.
	* A hash map mapping from the original node to its counterpart takes $O(|V|)$.
#### 2. BFS
BFS goes as broad as possible at first. For each currently visited node, we complete the **outlinks** to all its neighbors. One important thing to note is that two nodes can share the same neighbor, which might lead to duplicated pushes of the same node into the queue. Here, we just use the existing hash map to make sure we won't push the same node into the queue twice.
```python
def cloneGraph(node: Optional['Node']) -> Optional['Node']:
    if node is None:
        return None

    o2n = {node: Node(node.val)}
    q = deque([node])
    while len(q) != 0:
        visited = q.popleft()
        clone = o2n[visited]

        for nei in visited.neighbors:
            if nei not in o2n:
                o2n[nei] = Node(nei.val)
                q.append(nei)

            clone.neighbors.append(o2n[nei])

    return list(o2n.values())[0]
```
* Time complexity: $O(|V| + |E|)$, where $|V|$ is the number of nodes and $|E|$ is the number of edges.
	* Traversing a graph means visiting all the nodes are steps through all the edges, which takes $O(|V| + |E|)$ to complete.
* Space complexity: $O(|V| + |E|)$
	* The input and output graph take $O(|V| + |E|)$.
	* A queue storing the following nodes to visit takes $O(|V|)$.
	* A hash map mapping from the original node to its counterpart takes $O(|V|)$.

