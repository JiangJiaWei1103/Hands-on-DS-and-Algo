# [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)
> Find the kth smallest element in a BST. Note that k is **1-indexed**.
### Input
* `root`: Root of a BST.
### Output
The kth smallest value.
### Example
```
Input: root = [3, 1, 4, null, 2], k = 1
Output: 1
```
### Idea
Note that **inorder traversal** of a BST results in a sorted value sequence from the smallest to the greatest.
#### 1. Recursive with Traversal List
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
	* Each node is visited once.
* Space complexity: $O(n)$
	* Store the result of inorder traversal.
	* The depth of call stack takes $O(h)$, where $h$ is the height of a BST ($n$ in the worst case).
##### Comment
* Use additional variables `k` to track how many remaining visits are needed and `kth_ele` to record the answer.
```python
class Solution:
    """Recursive DFS w/ early stopping.
    
    Attributes:
        k: An integer tracking how many remaining visits are needed.
        kth_ele: An integer representing the kth smallest element.
    """

    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        self.k = k
        self.kth_ele = 0
        self._dfs(root)

        return self.kth_ele

    def _dfs(self, root: Optional[TreeNode]) -> None:
        if root is None or self.k < 0:
            return None

        self._dfs(root.left)
        self.k -= 1
        if self.k == 0:
            self.kth_ele = root.val
            return
        self._dfs(root.right)
```
* Space complexity: $O(h)$, where $h$ is the height of a BST ($n$ in the worst case)
#### 2. Iterative with Early Stopping
Use a stack to simulate implicit call stack in recursion. Also, early stop the process if the kth smallest element has been found.
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
            # Early stop when the kth smallest ele is found
            return visited.val

        root = visited.right
```
* Time complexity: $O(n)$
	* Each node is visited once.
	* Early stop when the kth smallest element is found.
* Space complexity: $O(h)$, where $h$ is the height of a BST ($n$ in the worst case)