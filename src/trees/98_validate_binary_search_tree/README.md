# [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
> Determine if a binary tree is a valid BST.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[1, 10^4]`.
	* `-2^31 <= Node.val <= 2^31 - 1`
### Output
True if the binary tree is a valid BST else False.
### Example
```
Input: root = [2,1,3]
Output: true
```
### Idea
#### 1. Recursive - DFS
Checking if a tree is a valid binary search tree is just a process of checking whether each node **fits in the given boundaries** (i.e., lower and upper). Every time we go down to the left subtree, the upper bound drops. When we go right, the lower bound rises.

[![98-validate-bst.png](https://i.postimg.cc/9FncmXFX/98-validate-bst.png)](https://postimg.cc/F73MG4Lw)

For each node to check, we need to make sure that,
1. The current node falls into the correct interval.
2. The left subtree is valid.
3. The right subtree is valid.
```python
def isValidBST(root: Optional[TreeNode]) -> bool:
    def _dfs(root: Optional[TreeNode], path_min: float, path_max: float) -> bool:
        if root is None:
            return True

        return (
            (path_min < root.val < path_max)
            and _dfs(root.left, path_min, root.val)
            and _dfs(root.right, root.val, path_max)
        )

    return _dfs(root, float("-inf"), float("inf"))
```
* Time complexity: $O(n)$
	* Visiting each node in the BST takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the BST.
	* The worst case happens when the BST is **skewed**, which has a call stack of depth $n$.
##### Comment
*  Feel free to try preorder, inorder, and postorder traversal.
#### 2. Iterative - BFS
The core logic is just the same as DFS,
1. Check if the visited node falls into the correct interval.
2. Check if subtrees are valid with the updated boundaries.

For implementation, we use a queue to store nodes to visit and the corresponding boundaries.
```python
def isValidBST(root: Optional[TreeNode]) -> bool:
    q = deque([(root, float("-inf"), float("inf"))])
    while len(q) > 0:
        node, lo, up = q.popleft()
        if lo < node.val < up:
            if node.left is not None:
                q.append((node.left, lo, node.val))
            if node.right is not None:
                q.append((node.right, node.val, up))
        else:
            return False

    return True
```
- Time complexity: $O(n)$
    - Visiting each node in the binary tree takes $O(n)$.
- Space complexity: $O(n)$
    - A queue storing the node to check and the max value along the path takes $O(n)$ in the worst case, in which the binary tree is **balanced**
#### 3. Inorder Traversal
This can be the most intuitive solution. We use the property that **the inorder traversal of a BST is strictly increasing**. Following provides both iterative and recursive solutions. The core concept is to go down to the leftmost node first and return back with the previous node value acting as the lower bound.
```python
def isValidBST(root: Optional[TreeNode]) -> bool:
    def _dfs(root: Optional[TreeNode], prev: float) -> float:
        if root is None:
            return prev

        prev = _dfs(root.left, prev)
        if root.val > prev:
            return _dfs(root.right, root.val)
        else:
            return float("inf")

    return _dfs(root, float("-inf")) != float("inf")


def isValidBST(root: Optional[TreeNode]) -> bool:
    prev, stack = float("-inf"), []
    while root is not None or len(stack) > 0:
        while root is not None:
            stack.append(root)
            root = root.left

        visited = stack.pop()
        if visited.val <= prev:
            return False

        prev = visited.val
        root = visited.right

    return True
```
* Time complexity: $O(n)$
	* Visiting each node in the BST takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the BST.
	* The worst case happens when the BST is **skewed**, which has a call stack of depth $n$.
##### Comment
* For recursion, we can define a `prev` node outside `_dfs` and keep updating it inside `_dfs` during the traversal. Here, we adopt a tricky method to return `inf` if the check fails.