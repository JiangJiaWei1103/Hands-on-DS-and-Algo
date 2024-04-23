# [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)
> Check if a binary tree is symmetric around its center line.
### Input
* `root`:  Root of a binary tree.
### Output
`True` if a binary tree is symmetry else `False`.
### Example
```
Input: root = [1,2,2,3,4,4,3]
Output: true
```
### Idea
#### 1. Recursive - DFS
Use two-pointer-like concept to compare nodes on left and right sides at the same time.
```python
def isSymmetric(root: Optional[TreeNode]) -> bool:
    def _eq(l: Optional[TreeNode], r: Optional[TreeNode]) -> bool:
        if l is None and r is None:
            return True
        elif l and r and l.val == r.val:
            return _eq(l.left, r.right) and _eq(l.right, r.left)
        else:
            return False

    if root is None:
        return True
    return _eq(root.left, root.right)
```
* Time complexity: $O(n)$
	* Comparison is taken for about $\frac{n}{2}$ times.
* Space complexity: $O(h)$, where $h$ denotes the height of a tree
	* $O(log\ n)$ for a balanced tree.
	* $O(n)$ for a highly skewed tree.
##### Comment
* It's also possible to use a stack to explicitly run DFS above.
#### 2. Iterative - BFS
Use a queue to do breadth-first checking.
```python
def isSymmetric(root: Optional[TreeNode]) -> bool:
    if root is None: return True

    q = deque([root.left, root.right])
    while len(q) != 0:
        l, r = q.popleft(), q.popleft()

        if l is None and r is None:
            continue
        elif l is None or r is None or l.val != r.val:
            return False
        
        q.extend([l.left, r.right, l.right, r.left])

    return True
```
* Time complexity: $O(n)$
	* Comparison is taken for about $\frac{n}{2}$ times.
* Space complexity: $O(n)$
	* For a balanced binary tree, the number of nodes at the last level is equal to half of the total number of nodes, hence $O(n)$.