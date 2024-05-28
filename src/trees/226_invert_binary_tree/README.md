# [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)
> Invert a binary tree.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[0, 100]`.
	* `-100 <= Node.val <= 100`
### Output
Root of the inverted binary tree.
### Example
```
Input: root = [4, 2, 7, 1, 3, 6, 9]
Output: [4, 7, 2, 9, 6, 3, 1]
```
### Idea
#### 1. Recursive - DFS
To invert a binary tree, we can imagine a process of keeping swapping left and right children of the current `root`. Note that each subtree is also a binary tree itself, so each node will act as `root` exactly once during this process.
```python
def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if root is None:
        return None

    root.left, root.right = root.right, root.left
    invertTree(root.left)
    invertTree(root.right)

    return root


def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if root is None:
        return None

    root.left, root.right = invertTree(root.right), invertTree(root.left)
    
    return root
```
* Time complexity: $O(n)$
	* Visiting every node in the binary tree takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the binary tree.
	* The worst case happens when the binary tree is extremely unbalanced, which has a call stack of depth $n$. Hence, it takes $O(n)$ space complexity.
#### 2. Iterative - BFS
In addition to the recursive approach, we can also solve the problem iteratively by **level-order traversal**. Concretely speaking, a queue (or a stack) is used as an auxiliary data structure to record nodes level by level.<br>
Both queue and stack can be used, because we swap left and right children of each visited node first. At this point, there's no difference to handle the swapped left child first or the right first.
```python
def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if root is None:
        return None

    q = deque([root])
    while len(q) != 0:
        visited = q.popleft()
        visited.left, visited.right = visited.right, visited.left
        if visited.left is not None:
            q.append(visited.left)
        if visited.right is not None:
            q.append(visited.right)

    return root
```
* Time complexity: $O(n)$
	* Visiting every node in the binary tree takes $O(n)$.
* Space complexity: $O(n)$
	* A queue storing nodes level by level takes $O(n)$.