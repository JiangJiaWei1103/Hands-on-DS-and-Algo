# [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
> Find the lowest common ancestor (LCA) of two nodes in a binary tree.
* The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).
### Input
* `root`: Root of a binary tree.
* `p`: The first node.
* `q`: The second node.
### Output
The LCA of a node pair (`p`, `q`) in a binary tree.
### Example
```
Input: root = [3, 5, 1, 6, 2, 0, 8, null, null, 7, 4], p = 5, q = 1
Output: 3
Explanation: The LCA of nodes 5 and 1 is 3.
```
### Idea
Reformulate the problem as **finding a pivot node at which two given nodes can be found with the minimum efforts**.
#### 1. Recursive by DFS
Suppose you're standing at a pivot node `pivot`, there exist three conditions summarized as follows,
1. If either `p` or `q` is where you're standing, then the LCA is found.
2. If either `p` or `q` is found in left or right subtree, and the other is found in the opposite, then where you're standing is the LCA.
3. If `p` and `q` are on the same side (either left or right), then the LCA can be found on that side.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if root in {p, q, None}:
        return root

    # Find candidate LCAs in left and right subtrees
    lca_left = lowestCommonAncestor(root.left, p, q)
    lca_right = lowestCommonAncestor(root.right, p, q)

    if lca_left is not None and lca_right is not None:
        # p and q are found on different subtrees
        return root
    elif lca_left is not None:
        # p and q are in the left subtree
        return lca_left
    else:
        # p and q are in the right subtree
        return lca_right
```
* Time complexity: $O(n)$
	* Each node is visited once.
* Space complexity: $O(h)$, where $h$ is the height of the tree
	* The depth of call stack is $h$.
#### 2. Iterative w/ Parent Hash Map
Use a parent hash map to record each node's parent. Then, the path to root from any node can be derived (*i.e.,* from any node to its ancestors).

Because the LCA is searched from leaf  to root, the first match must be LCA, hence guaranteeing that the LCA is **the lowest**.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if root in {p, q}:
        return root 

    qu = deque([root])
    node2parent = {root: None}
    while len(qu) > 0:
        visited = qu.popleft()
        if visited.left is not None:
            qu.append(visited.left)
            node2parent[visited.left] = visited
        if visited.right is not None:
            qu.append(visited.right)
            node2parent[visited.right] = visited

    # Find LCA by tracing parent path from leaf to root 
    cand_lcas = set()
    while p:
        cand_lcas.add(p)
        p = node2parent[p]
    while q not in cand_lcas:
        q = node2parent[q]

    return q
```
* Time complexity: $O(n)$
	* Each node is visited once.
	* LCA searching takes at most $n$ times (think about a linear BT).
* Space complexity: $O(n)$
	* Store parent node for each node.