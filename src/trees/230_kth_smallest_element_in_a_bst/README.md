# [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)
> Find the kth smallest element in a BST. Note that k is **1-indexed**.
### Input
* `root`: Root of a BST.
	* The number of nodes in the tree is `n`.
	* `1 <= k <= n <= 10^4`
	* `0 <= Node.val <= 10^4`
### Output
The kth smallest value.
### Example
```
Input: root = [3, 1, 4, null, 2], k = 1
Output: 1
```
### Idea
The core idea is to leverage the property that the inorder traversal of a binary tree is **strictly increasing**.
#### 1. Inorder Traversal with Retrieval
The most intuitive solution is to construct the inorder traversal list and return the `(k-1)-th` element.
```python
def kthSmallest(root: Optional[TreeNode], k: int) -> int:
    def _dfs(root: Optional[TreeNode]) -> None:
        if root is None:
            return None

        _dfs(root.left)
        inorder_traversal.append(root.val)
        _dfs(root.right)

    inorder_traversal = []
    _dfs(root)

    return inorder_traversal[k-1]
```
* Time complexity: $O(n)$
	* Visiting each node in the BST takes $O(n)$.
* Space complexity: $O(n)$
	* A list storing the inorder traversal takes $O(n)$.
	* The depth of call stack takes $O(h)$, but $O(n)$ in the worst case.
#### 2. Inorder Traversal with Early Stop
Instead of traversing the entire BST, we can stop the traversal right after the kth smallest element is found. Following provides both iterative and recursive solutions.
```python
def kthSmallest(root: Optional[TreeNode], k: int) -> int:
    stack = []
    while root is not None or len(stack) > 0:
        while root is not None:
            stack.append(root)
            root = root.left

        visited = stack.pop()
        k -= 1
        if k == 0:
            return visited.val
        
        root = visited.right


def kthSmallest(root: Optional[TreeNode], k: int) -> int:
    def _dfs(root: Optional[TreeNode]) -> None:
        nonlocal k, kth

        if root is None or k < 0:
            return
        
        _dfs(root.left)
        k -= 1
        if k == 0:
            kth = root.val
            return
        _dfs(root.right)

    kth = None
    _dfs(root)

    return kth
```
* Time complexity: $O(n)$
	* Visiting each node in the BST takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of the BST.
	* The worst case happens when the BST is **skewed**, which has a (call) stack of depth $n$.
##### Comment
* In the recursive solution, we use `nonlocal` keyword to manipulate the local variable in the nested function. After we make sure that kth is found, direct returning will halt the searching.