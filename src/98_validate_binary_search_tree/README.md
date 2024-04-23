# [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
> Determine if a binary tree is a valid BST.
### Input
* `root`: Root of a binary tree.
### Output
`True` if the binary tree is a valid BST else `False`.
### Example
```
Input: root = [2,1,3]
Output: true
```
### Idea
#### 1. Recursive - Lower/Upper Bound
Recursively update the lower and upper bounds along each path in a BST and check if the visited node falls in the correct range.
[![98-validate-bst.png](https://i.postimg.cc/9FncmXFX/98-validate-bst.png)](https://postimg.cc/F73MG4Lw)
1. Check if the visited node falls in the correct range, then return `False` if failed.
2. Update the **upper bound** if traversing the **left** subtree.
3. Update the **lower bound** if traversing the **right** subtree.
4. Return `True` when one path is completed. 
```python
def isValidBST(root: Optional[TreeNode]) -> bool:
    def _isvalid(
        root: Optional[TreeNode], 
        lower: float = -float("inf"), 
        upper: float = float("inf")
    ) -> bool:
        if root is None:
            return True
        elif root.val <= lower or root.val >= upper:
            return False
        else:
            return (
                _isvalid(root.left, lower, min(root.val, upper))
                and _isvalid(root.right, max(lower, root.val), upper)
            )
            # or
            # return (
            #     _isvalid(root.left, lower, root.val)
            #     and _isvalid(root.right, root.val, upper)
            # )

    return _isvalid(root)
```
* Time complexity: $O(n)$
	* Each node in a BST is visited at most once.
* Space complexity: $O(h)$, where $h$ is the height of a BST
##### Comment
* `mix` and `max` evaluations can be removed, because `root.val` will be the new upper bound for the left branch and the new lower bound for the right one.
#### 2. Iterative - Inorder Traversal
Use the concept that the inorder traversal of a valid BST will be a **strictly increasing** value sequence.
```python
def isValidBST(root: Optional[TreeNode]) -> bool:
    if root is None:
        return True

    prev = float("-inf")
    stack = []
    while root is not None or len(stack) > 0:
        while root is not None:
            stack.append(root)
            root = root.left

        visited = stack.pop()
        if prev >= visited.val:
            return False
        
        prev = visited.val
        root = visited.right

    return True
```
* Time complexity: $O(n)$
	* Each node in a BST is visited at most once.
* Space complexity: $O(h)$, where $h$ is the height of a BST