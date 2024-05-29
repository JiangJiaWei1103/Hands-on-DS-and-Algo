# [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)
> Determine whether a binary tree is height-balanced or not.
* A **height-balanced** binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[0, 5000]`.
	- `-10^4 <= Node.val <= 10^4`
### Output
`True` if the binary tree is height-balanced else `False`.
### Example
```
Input: root = [3, 9, 20, null, null, 15, 7]
Output: true
```
### Idea
The check if a binary tree is balanced for a single node (take 3 in the image as an example), we need to determine whether the difference of depths of two subtrees exceeds 1. Hence, the problem can be formulated as,
```
balanced = abs(depth(root.left), depth(root.right)) <= 1
```
[![110.png](https://i.postimg.cc/PJXhDnSX/110.png)](https://postimg.cc/XGzTMmMm)
#### 1. Top-Down (Brute-Force)
The most intuitive solution is to run this check **for each node** in the tree, which can be thought of as a nested loop. For the outer, we need to traverse each node in the tree. As for the inner, we need to determine whether the currently visited node passes balance check or not.<br>
Note that the checking is done in a **top-down** manner, because we check the currently visited node first, then go one level deeper to check subtrees.
```python
def isBalanced(root: Optional[TreeNode]) -> bool:
    def _get_depth(root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        return 1 + max(_get_depth(root.left), _get_depth(root.right))

    if root is None:
        return True
    
    # Run balance check for the visited node
    l_depth = _get_depth(root.left)
    r_depth = _get_depth(root.right)
    if abs(l_depth - r_depth) > 1:
        return False

    return isBalanced(root.left) and isBalanced(root.right)
```
* Time complexity: $O(n^2)$
	* Running balance check (which takes $O(n)$ itself) for each node takes $O(n^2)$.
* Space complexity: $O(n)$ <span style="color:orange;">(?)</span>
#### 2. Bottom-Up DFS
From problem formulation, we can see that balance checking uses depths of two subtrees. To avoid re-computing the depth for each node, we instead run balance check in a bottom-up manner (from leaf to root).

[![110-2.png](https://i.postimg.cc/JzJc2sN2/110-2.png)](https://postimg.cc/QF8TBxnQ)

The process is summarized as follows,
1. Go down as deep as we can.
2. Return the current depth (actually height of the subtree).
3. Run balance check.
4. Backtrack by recursively doing steps 2 - 3.
```python
def isBalanced(root: Optional[TreeNode]) -> bool:
    def _dfs(root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        l_depth = _dfs(root.left)
        r_depth = _dfs(root.right)
        if abs(l_depth - r_depth) > 1:
            is_balanced[0] = False

        return 1 + max(l_depth, r_depth)

    is_balanced = [True] 
    _dfs(root)

    return is_balanced[0]
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: 𝑂(ℎ), where ℎ is the height of the binary tree.
	* The worst case happens when the binary tree is extremely unbalanced, which has a call stack of depth 𝑛. Hence, it takes 𝑂(𝑛) space complexity.
##### Comment
* Can we implement this idea with an iterative approach?