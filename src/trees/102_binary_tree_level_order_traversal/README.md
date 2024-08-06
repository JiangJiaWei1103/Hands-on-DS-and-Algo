# [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
> Return the level order traversal of a binary tree.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[0, 2000]`.
	* `-1000 <= Node.val <= 1000`
### Output
Level order traversal of the binary tree.
### Example
```
Input: root = [3, 9, 20, null, null, 15, 7]
Output: [[3], [9, 20], [15, 7]]
```
### Idea
#### 1. Iterative - BFS
To implement the level order traversal, the most intuitive way is to use BFS. We group elements on the same level to make sure that elements are visited **level by level.**
```python
def levelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    if root is None:
        return []

    res = []
    q = deque([root])
    while len(q) > 0:
        cur_lv = []
        for i in range(len(q)):
            visited = q.popleft()
            cur_lv.append(visited.val)
            if visited.left is not None:
                q.append(visited.left)
            if visited.right is not None:
                q.append(visited.right)

        res.append(cur_lv)

    return res
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(n)$
	* A queue storing nodes level by level takes $O(n)$ in the worst case, in which the binary tree is **perfect**.
#### 2. Recursive - DFS
In addition, DFS is also a feasible way to solve this problem. What's important is that we need to keep track of the level we're currently processing to make sure each node is recorded on the corresponding level. Note that we append an empty list to the result list each time we enter a new level.
```python
def levelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    def _dfs(root: Optional[TreeNode], lv: int) -> None:
        if root is None:
            return None
        
        if len(res) == lv:
            res.append([])
        res[lv].append(root.val)
        _dfs(root.left, lv + 1)
        _dfs(root.right, lv + 1)

    res = []
    _dfs(root, 0)

    return res
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the binary tree.
	* The worst case happens when the binary tree is **skewed**, which has a call stack of depth $n$.