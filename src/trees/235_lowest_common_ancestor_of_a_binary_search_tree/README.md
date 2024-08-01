# [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
> Find the lowest common ancestor (LCA) node of two nodes in a BST.
* The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).
### Input
* `root`: Root of a binary search tree.
	* The number of nodes in the tree is in the range `[2, 10^5]`.
	* `-10^9 <= Node.val <= 10^9`
	* All `Node.val` are **unique**.
* `p`: The first node.
* `q` The second node.
	* `p != q`
	* `p` and `q` will exist in the BST.
### Output
The lowest common ancestor (LCA) of two nodes.
### Example
```
Input: root = [6, 2, 8, 0, 4, 7, 9, null, null, 3, 5], p = 2, q = 8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.
```
### Idea
Instead of taking $O(n)$ to visit each node in the BST, we can further reduce the time complexity to the height of the BST by leveraging BST properties, which are summarized as follows,
1. If both values of `p` and `q` are **smaller** than the root, return the LCA from the **left** child.
2. If both values of `p` and `q` are **greater** than the root, return the LCA from the **right** child.
3. If one value is smaller than the root and the other is greater, the root is the LCA.
#### 1. (Deprecated) See [[Hack-the-Algo-and-DS/Grind-LeetCode/src/trees/236_lowest_common_ancestor_of_a_binary_tree/README|236]] for Detailed Solution
The solution can be thought of as a child-searching process by an ancestor (*i.e.,* the current root). Below illustrates how searching looks like, and there are 4 conditions to consider,
1. `root in {p, q}`: If an ancestor itself is one of the nodes, `root` is the LCA.
	* *e.g.,* `2` in the figure.
2. `r_lca is None`: If `p` and `q` can't be found in the **right** subtree, the LCA must come from the **left** subtree.
	* *e.g.,* `6` in the figure.
3. `l_lca is None`: If `p` and `q` can't be found in the **left** subtree, the LCA must come from the **right** subtree.
4. `l_lca is not None and r_lca is not None`: `p` and `q` are found in the left or right subtree respectively, `root` is the LCA.

Finally, we need to consider another base case. If the searching process goes down to `None`, return `None` directly, because no node can be found.

[![235.png](https://i.postimg.cc/Y0FtYq1n/235.png)](https://postimg.cc/kRnrdCYK)

```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if root in {p, q}:
        return root 
    if root is None:
        return None

    l_lca = lowestCommonAncestor(root.left, p, q)
    r_lca = lowestCommonAncestor(root.right, p, q)
    if r_lca is None:
        return l_lca
    elif l_lca is None:
        return r_lca
    else:
        return root
```
* Time complexity: $O(n)$
	* Visiting each node in the binary search tree takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the BST.
	* The worst case occurs when the BST is skewed, in which the depth of call stack takes $O(h) \approx O(n)$.
#### 2. Recursive - DFS
The base case defined in the first implementation can be handled by the third condition. Note that we don't need to worry about the case in which the searching goes down to the leaf `None`, because the LCA will be returned beforehand.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if root in {p, q}:
        return root

    if p.val < root.val and q.val < root.val:
        return lowestCommonAncestor(root.left, p, q)
    elif p.val > root.val and q.val > root.val:
        return lowestCommonAncestor(root.right, p, q)
    else:
        return root


def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if p.val < root.val and q.val < root.val:
        return lowestCommonAncestor(root.left, p, q)
    elif p.val > root.val and q.val > root.val:
        return lowestCommonAncestor(root.right, p, q)
    else:
        return root
```
* Time complexity: $O(h)$, where $h$ is the height of the BST.
	* Halving the binary search tree during searching takes $O(log\ n)$ in total.
	* The worst case happens when the BST is **skewed**, which takes $O(n)$ in total.
* Space complexity: $O(h)$, where $h$ is the height of the BST.
	* The worst case occurs when the BST is **skewed**, in which the depth of call stack takes $O(h) \approx O(n)$.
#### 3.  Iterative
We can just convert idea 2 into an iterative implementation. For the first one, the queue takes actually $O(1)$ because it always stores one element during searching. Hence, we can remove it to keep the implementation concise.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    qu = deque([root])
    while len(qu) > 0:
        visited = qu.popleft()
        if p.val < visited.val and q.val < visited.val:
            qu.append(visited.left)
        elif p.val > visited.val and q.val > visited.val:
            qu.append(visited.right)
        else:
            return visited


def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if p.val < root.val and q.val < root.val:
        return lowestCommonAncestor(root.left, p, q)
    elif p.val > root.val and q.val > root.val:
        return lowestCommonAncestor(root.right, p, q)
    else:
        return root
```
* Time complexity: $O(h)$, where $h$ is the height of the BST.
	* Halving the binary search tree during searching takes $O(log\ n)$ in total.
	* The worst case happens when the BST is **skewed**, which takes $O(n)$ in total.
* Space complexity: $O(1)$