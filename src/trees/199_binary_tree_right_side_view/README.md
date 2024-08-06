# [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)
> Return the right side view of a binary tree.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[0, 100]`.
	* `-100 <= Node.val <= 100`
### Output
The right side view of the binary tree.
### Example
```
Input: root = [1,2,3,null,5,null,4]
Output: [1,3,4]
```
### Idea
The core idea is to record the **rightmost** element on each level.
#### 1. Iterative - BFS
The most intuitive way to do the level order traversal is to use BFS. We use a queue to store elements on the same level as a group and record the **rightmost** element for each level. Because we append children in a left-first manner, the last visited node on each level is the **rightmost**.
```python
def rightSideView(root: Optional[TreeNode]) -> List[int]:
    if root is None:
        return []

    res = []
    q = deque([root])
    while len(q) > 0:
        lv_len = len(q)
        for i in range(lv_len):
            visited = q.popleft()
            if i == lv_len - 1:
                res.append(visited.val)

            if visited.left is not None:
                q.append(visited.left)
            if visited.right is not None:
                q.append(visited.right)

    return res
```
* Time complexity: $O(n)$
	* Visiting each element in the binary tree takes $O(n)$.
* Space complexity: $O(n)$
	* A queue storing nodes level by level takes $O(n)$ in the worst case, in which the binary tree has $\frac{n+1}{2}$ leaves.
##### Comment
* Leveraging list comprehension, we can solve this problem in a more concise way. The process is summarized as follows,
	1. Record the last (i.e., the **rightmost**) element of each level.
	2. Override the queue with elements on the next level.
	3. Keep running 1 and 2 until the last level is completed.
```python
def rightSideView(root: Optional[TreeNode]) -> List[int]:
    if root is None:
        return []

    res = []
    q = [root]
    while len(q) > 0:
        res.append(q[-1].val)
        q = [child for node in q for child in (node.left, node.right) if child is not None]

    return res
```
#### 2. Recursive - DFS
DFS implementation is also feasible. There are two keys in this solution,
1. Keep track of the level information.
2. Search from **right to left** to make sure the **rightmost** can be visited as the **first** element of that level.
```python
def rightSideView(root: Optional[TreeNode]) -> List[int]:
    def _dfs(root: Optional[TreeNode], lv: int) -> None:
        if root is None:
            return None
        
        if len(res) == lv:
            res.append(root.val)
        _dfs(root.right, lv + 1)
        _dfs(root.left, lv + 1)

    res = []
    _dfs(root, 0)

    return res
```
* Time complexity: $O(n)$
	* Visiting each element in the binary tree takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the binary tree.
	* The worst case happens when the binary tree is **skewed**, which has a call stack of depth $n$.