# [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
> Find the lowest common ancestor (LCA) of two nodes in a binary tree.
* The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[2, 10^5]`.
	* `-10^9 <= Node.val <= 10^9`
	* All `Node.val` are **unique**.
* `p`: The first node.
* `q`: The second node.
	* `p != q`
	* `p` and `q` will exist in the tree.
### Output
The LCA of nodes `p` and `q`.
### Example
```
Input: root = [3, 5, 1, 6, 2, 0, 8, null, null, 7, 4], p = 5, q = 1
Output: 3
Explanation: The LCA of nodes 5 and 1 is 3.
```
### Idea
Before diving into the possible solutions, we first discuss the core logic to solve the problem. For a given root, the LCA of `p` and `q` falls into one of the following two cases,
1. If `p` and `q` are on the same child, then the one close to the root is the LCA.
2. If `p` is on the one side and `q` on the other, the LCA is the root itself.
#### 1. Recursive - DFS
In recursion, we keep returning the candidate LCA from child to parent. Combined with the concept described above, there exist three conditions in the core logic,
1. If either side returns a candidate LCA, return it directly.
2. If both sides return a candidate LCA, return the current `root`.
3. If both sides return `None`, return `None` directly.

In addition, we define the base cases as follows,
1. If the recursion goes down to the leaf `None`, return `None`.
2. If the current `root` matches either `p` or `q`, return it as the candidate LCA.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    # Base case
    if root in {None, p, q}:
        return root

    l_lca = lowestCommonAncestor(root.left, p, q)
    r_lca = lowestCommonAncestor(root.right, p, q)
    if l_lca is None:
        return r_lca
    elif r_lca is None:
        return l_lca
    else:
        return root
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the binary tree.
	* The depth of call stack is $h$. The worst case happens when the binary tree is **skewed**, which takes $O(n)$.
#### 2. Iterative - BFS with a Hash Map
To search the LCA using BFS, we need to map each child to its parent to construct a path from the descendant to the ancestor. Note that the LCA is searched from leaf to root, the first match must be the LCA, hence guaranteeing that the LCA is **the lowest**.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    c2p = {root: None}
    qu = deque([root])
    while len(qu) > 0:
        visited = qu.popleft()
        if visited.left is not None:
            qu.append(visited.left)
            c2p[visited.left] = visited
        if visited.right is not None:
            qu.append(visited.right)
            c2p[visited.right] = visited

    # Create a child to parent path using either node
    cand_lcas = set()
    child = p
    while child is not None:
        cand_lcas.add(child)
        child = c2p[child]

    # Search LCA with the other
    lca = q
    while lca not in cand_lcas:
        lca = c2p[lca]

    return lca
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
	* The worst case of LCA searching happens when the binary tree is **skewed**, which takes $O(n)$.
* Space complexity: $O(n)$
	* A queue storing nodes during traversal takes $O(n)$ when the binary tree is **perfect**.
	* A hash map storing the child to parent relations takes $O(n)$.
#### 3. [Iterative with Path-Building Technique](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/solutions/65245/iterative-solutions-in-python-c/)
