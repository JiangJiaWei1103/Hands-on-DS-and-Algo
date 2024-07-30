# [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
> Return the maximum depth of a binary tree.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[0, 10^4]`.
	* `-100 <= Node.val <= 100`
### Output
The maximum depth of the binary tree.
### Example
```
Input: root = [3, 9, 20, null, null, 15, 7]
Output: 3
```
### Idea
#### 1. Iterative - BFS
Because we need to derive the height of a binary tree, the most intuitive solution on my mind is **level-order traversal**. Through traversing the binary tree level by level, we keep incrementing the depth by 1 if the current level is completed. To be concrete, we treat each level as a group during traversal.
```python
def maxDepth(root: Optional[TreeNode]) -> int:
    if root is None:
        return 0

    depth = 0
    q = deque([root])
    while len(q) > 0:
        for i in range(len(q)):
            visited = q.popleft()
            if visited.left is not None:
                q.append(visited.left)
            if visited.right is not None:
                q.append(visited.right)

        depth += 1

    return depth
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(n)$
	* A queue storing nodes level by level takes 𝑂(𝑛). The worst case occurs when the binary tree is **perfect**, which has $\frac{n+1}{2}$ leaves.
#### 2. Recursive - DFS
The height of a binary tree can be formulated by the following relationship,
```
max_depth = 1 + max(max_depth_of_left_child, max_depth_of_right_child)
```
Hence, we can solve this problem by a simple recursive function. The base case is triggered when the current `root` is `None`, which won't contribute to the overall height.
```python
def maxDepth(root: Optional[TreeNode]) -> int:
    if root is None:
        return 0
    
    lmax_depth = maxDepth(root.left)
    rmax_depth = maxDepth(root.right)
    max_depth = 1 + max(lmax_depth, rmax_depth)

    return max_depth


def maxDepth(root: Optional[TreeNode]) -> int:
    if root is None:
        return 0

    # Or, directly return it
    depth = 1 + max(maxDepth(root.left), maxDepth(root.right)) 

    return depth
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: 𝑂(ℎ), where ℎ is the height of the binary tree.
	* The worst case happens when the binary tree is **skewed**, which has a call stack of depth 𝑛. Hence, it takes 𝑂(𝑛) space complexity.
### References
* [Queue in Python](https://www.geeksforgeeks.org/queue-in-python/)
* [What is the difference between depth and height in a tree?](https://stackoverflow.com/questions/2603692/what-is-the-difference-between-depth-and-height-in-a-tree)
	* Note the definition of depth and height.