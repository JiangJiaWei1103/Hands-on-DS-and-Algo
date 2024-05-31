# [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
> Return the level order traversal of a binary tree.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[0, 2000]`.
	* `-1000 <= Node.val <= 1000`
### Output
Level order traversal of nodes' values of the BST.
### Example
```
Input: root = [3, 9, 20, null, null, 15, 7]
Output: [[3], [9, 20], [15, 7]]
```
### Idea
#### 1. BFS
For a level order traversal, it's quite straightforward to use an iterative BFS solution. We use a queue to store nodes and visit them **level by level**.
```python
def levelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    if root is None:
        return []

    q = deque([root])
    res = []
    while len(q) > 0:
        res.append([])
        for _ in range(len(q)):
            visited = q.popleft()
            res[-1].append(visited.val)

            if visited.left is not None:
                q.append(visited.left)
            if visited.right is not None:
                q.append(visited.right)
    
    return res
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(n)$
	* The traversal result takes $O(n)$.
	* A queue tracking nodes level by level takes $O(n)$.
#### 2. DFS
We can also use DFS to solve this problem, which seems counterintuitive at the first glance. For DFS, we use another variable `depth` to track which level the visited node is located.
```python
def levelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    def _dfs(root: Optional[TreeNode], depth: int) -> None:
        if root is None:
            return None
        
        if len(res) == depth:
            res.append([])
        res[depth].append(root.val)
        _dfs(root.left, depth + 1)
        _dfs(root.right, depth + 1)

    res = []
    _dfs(root, 0)

    return res
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(n)$
	* The traversal result takes $O(n)$.
	* The depth of call stack takes $O(h)$ and $\approx O(n)$ in the worst case.