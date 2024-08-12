# [1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/)
> Count the number of good nodes in a binary tree.
* A node *X* in the tree is named **good** if in the path from root to *X* there are no nodes with a value *greater than X*.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the binary tree is in the range `[1, 10^5]`.
	* Each node's value is between `[-10^4, 10^4]`.
### Output
Number of good nodes in the binary tree.
### Example
```
Input: root = [3,1,4,3,null,1,5]
Output: 4
Explanation: Nodes in blue are good.
Root Node (3) is always a good node.
Node 4 -> (3,4) is the maximum value in the path starting from the root.
Node 5 -> (3,4,5) is the maximum value in the path
Node 3 -> (3,1,3) is the maximum value in the path.
```
### Idea
#### 1. Recursive - DFS
To check if a visited node is a good node or not, we need to track the current greatest node value along the path. During recursion, we keep updating the max value. Following provides two implementations,
1. Update the number of good nodes with a variable defined outside `_dfs`, then we don't need to return anything.
2. Define the number of good nodes with the relation `(root is good?) + #goods in the left subtree + #goods in the right subtree`.
```python
def goodNodes(root: TreeNode) -> int:
    def _dfs(root: Optional[TreeNode], path_max: int) -> None:
        if root is None:
            return
        
        if root.val >= path_max:
            n[0] += 1
        
        _dfs(root.left, max(path_max, root.val))
        _dfs(root.right, max(path_max, root.val))

    n = [0]
    _dfs(root, root.val)

    return n[0]


def goodNodes(root: TreeNode) -> int:
    def _dfs(root: Optional[TreeNode], path_max: int) -> int:
        if root is None:
            return 0
        
        good = 1 if root.val >= path_max else 0
        return (
            good
            + _dfs(root.left, max(path_max, root.val))
            + _dfs(root.right, max(path_max, root.val))
        )

    return _dfs(root, root.val)
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the binary tree.
	* The worst case happens when the binary tree is **skewed**, which has a call stack of depth $n$.
#### 2. Iterative - BFS
In addition, BFS is also a possible solution. Note that we need to keep track of the max value along the path to enable the good checking. Concretely speaking, we use a queue to store tuples `(node to check, max val along the path)`.
```python
def goodNodes(root: TreeNode) -> int:
    n = 0
    q = deque([(root, root.val)])
    while len(q) > 0:
        node, path_max = q.popleft()
        if node.val >= path_max:
            n += 1

        if node.left is not None:
            q.append((node.left, max(path_max, node.val)))
        if node.right is not None:
            q.append((node.right, max(path_max, node.val)))

    return n
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: $O(n)$
	* A queue storing the node to check and the max value along the path takes $O(n)$ in the worst case, in which the binary tree is **balanced**.