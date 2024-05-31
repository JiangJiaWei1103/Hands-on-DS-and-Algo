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
The lowest common ancestor (LCA) of the given node pair, `p` and `q`.
### Example
```
Input: root = [6, 2, 8, 0, 4, 7, 9, null, null, 3, 5], p = 2, q = 8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.
```
### Idea
#### 1. DFS w/o BST Property
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
#### 2. DFS w/ BST Property
Instead of taking $O(n)$ to visit each node in the BST, we can further reduce the time complexity by leveraging BST properties.<br>
To be concrete, we can continue to refine 4 conditions discussed in idea 1,
1. `root in {p, q}`: If an ancestor itself is one of the nodes, `root` is the LCA.
2. `p.val < root.val and q.val < root.val`: If values of both `p` and `q` are **smaller** than `root`, the LCA must come from the **left** subtree.
3. `p.val > root.val and q.val > root.val`: If values of both `p` and `q` are **greater** than `root`, the LCA must come from the **right** subtree.
4. `p.val < root.val < q.val or p.val > root.val > q.val`: `root`'s value is the **middle** value, `root` is the LCA.

Again, `None` base case should be considered.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if root in {p, q}:
        return root 
    if root is None:
        return None

    if p.val < root.val and q.val < root.val:
        return lowestCommonAncestor(root.left, p, q)
    elif p.val > root.val and q.val > root.val:
        return lowestCommonAncestor(root.right, p, q)
    else:
        return root
```
* Time complexity: $O(h)$, where $h$ is the height of the BST.
	* Halving the binary search tree during searching takes $O(log\ n)$.
	* The skewed BST still takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the BST.
	* The worst case occurs when the BST is skewed, in which the depth of call stack takes $O(h) \approx O(n)$.
##### Comment
* Diving deeper into the solution above, we can make the implementation more concise by combining cases.
	* The 1st and 4th conditions can be combined.
	* Because we return the LCA directly when the last condition is triggered, we don't need to worry about touching `None` of leaves.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    if p.val < root.val and q.val < root.val:
        return lowestCommonAncestor(root.left, p, q)
    elif p.val > root.val and q.val > root.val:
        return lowestCommonAncestor(root.right, p, q)
    else:
        return root
```
#### 3.  Iterative
We can just convert idea 2 into an iterative implementation.
```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    lca = root
    while True:
        if p.val < lca.val and q.val < lca.val:
            lca = lca.left
        elif p.val > lca.val and q.val > lca.val:
            lca = lca.right
        else:
            return lca
```
* Time complexity: $O(h)$, where $h$ is the height of the BST.
	* Halving the binary search tree during searching takes $O(log\ n)$.
	* The skewed BST still takes $O(n)$.
* Space complexity: $O(1)$