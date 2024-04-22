# [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
> Return the maximum depth of a binary tree.
### Input
* `root`: Root of a binary tree.
### Output
The maximum depth of a binary tree.
### Example
```
Input: root = [3, 9, 20, null, null, 15, 7]
Output: 3
```
### Idea
#### 1. Recursive - DFS
Consider the current depth as one plussed by the maximum depth of left and right subtrees.
```python
def maxDepth(root: Optional[TreeNode]) -> int:
    if root is None:
        return 0

    return 1 + max(maxDepth(root.left), maxDepth(root.right))
```
* Time complexity: $O(n)$
	* Each node is visited once.
* Space complexity: $O(h)$, where $h$ denotes the height of a tree
	* $O(log\ n)$ for a balanced tree.
	* $O(n)$ for a highly skewed tree.
#### 2. Iterative - BFS
Use a queue to keep track of nodes **at the same level** for each outer loop.
```python
def maxDepth(root: Optional[TreeNode]) -> int:
    if root is None: return 0

    max_depth = 0
    q = deque([root])
    while len(q) != 0:
        max_depth += 1
        for _ in range(len(q)):
            # Pop each node at the current level
            h = q.popleft()
            
            # Push nodes at the next level
            if h.left is not None:
                q.append(h.left)
            if h.right is not None:
                q.append(h.right)
    
    return max_depth
```
* Time complexity: $O(n)$
	* Each node is visited once (when popped).
* Space complexity: $O(n)$
	* For a balanced binary tree, the number of nodes at the last level is equal to half of the total number of nodes, hence $O(n)$ .
### References
* [Queue in Python](https://www.geeksforgeeks.org/queue-in-python/)