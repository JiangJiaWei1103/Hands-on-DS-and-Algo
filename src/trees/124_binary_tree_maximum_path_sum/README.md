# [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
> Compute the maximum path sum in a binary tree.
* A **path** in a binary tree is a sequence of nodes where each pair of adjacent nodes in the sequence has an edge connecting them. A node can only appear in the sequence **at most once**. Note that the path does not need to pass through the root.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[1, 3 * 10^4]`.
	* `-1000 <= Node.val <= 1000`
### Output
The maximum path sum of any possible (non-empty) path.
### Example
```
Input: root = [1,2,3]
Output: 6
Explanation: The optimal path is 2 -> 1 -> 3 with a path sum of 2 + 1 + 3 = 6.
```
### Idea
Analyzing outputs in different cases, we find that each node can act as two roles,
1. The branching point: The node (i.e., root of the subtree) is the highest node in the path.
2. The intermediate point: The node transfers (namely returns) the maximum gain to its parent (i.e., a possible better branching point).

Hence, the problem is simplified as picking the best branching point among all candidate branching points.
#### 1. Recursive - DFS
The core logic includes two parts, one handles the branching role and the other handles the intermediate role. For the former, we update the global max with the current max when the current root acts as a branching point. Considering there might exist better branching points, we need to transfer (return) the maximum gain till the current root to the parent, which handles the latter case.
```python
def maxPathSum(root: Optional[TreeNode]) -> int:
    def _dfs(root: Optional[TreeNode]) -> int:
        nonlocal max_sum

        if root is None:
            return 0

        max_gain_from_left = max(_dfs(root.left), 0)
        max_gain_from_right = max(_dfs(root.right), 0)
        max_sum_till_root = root.val + max_gain_from_left + max_gain_from_right
        max_sum = max(max_sum, max_sum_till_root)

        return root.val + max(max_gain_from_left, max_gain_from_right) 
    
    max_sum = root.val
    _dfs(root)

    return max_sum


def maxPathSum(root: Optional[TreeNode]) -> int:
    """Handle negative gain at return."""
    def _dfs(root: Optional[TreeNode]) -> int:
        nonlocal max_sum

        if root is None:
            return 0

        max_gain_from_left = _dfs(root.left)
        max_gain_from_right = _dfs(root.right)
        max_sum_till_root = max_gain_from_left + root.val + max_gain_from_right
        max_sum = max(max_sum, max_sum_till_root)

        return max(root.val + max(max_gain_from_left, max_gain_from_right), 0)
    
    max_sum = root.val
    _dfs(root)

    return max_sum
```
* Time complexity: $O(n)$
	* Visiting each node in the BT takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the BT.
	* The worst case happens when the BT is **skewed**, which has a call stack of depth $n$.
##### Comment
* Do you think it looks like Kadane's algorithm? Use `_so_far` to update the global max and `_ending_here` to explore potential better results.
```python
def maxPathSum(root: Optional[TreeNode]) -> int:
    def _dfs(root: Optional[TreeNode]) -> Tuple[int, int]:
        if root is None:
            return float("-inf"), 0

        left_sum_so_far, left_sum_ending_here = _dfs(root.left)
        right_sum_so_far, right_sum_ending_here = _dfs(root.right)
        root_sum_so_far = max(
            left_sum_so_far,
            right_sum_so_far,
            left_sum_ending_here + root.val + right_sum_ending_here 
        )

        return root_sum_so_far, max(root.val + max(left_sum_ending_here, right_sum_ending_here), 0)
    
    return _dfs(root)[0]
```
