# [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)
> Return the **inorder traversal** of a binary tree.
### Input
* `root`: Root of a binary tree.
### Output
Inorder traversal of tree's node values.
### Example
```
Input: root = [1, null, 2, 3]  # Level-order traversal
Output: [1, 3, 2]
```
### Idea
#### 1. Recursive
Traverse left subtree first until no left child, visit the root, then traverse the right.
```python
def inorderTraversal(root: Optional[TreeNode]) -> List[int]:
    """Recursive."""
    def _inorder(root: Optional[TreeNode]) -> None:
        if root is not None:
            _inorder(root.left)
            traversal.append(root.val)
            _inorder(root.right)

    traversal = []
    if root is not None:
        _inorder(root)

    return traversal
```
* Time complexity: $O(n)$
	* Visit each node once.
*  Space complexity: $O(n)$
	* Store the result of traversal.
	* Consider the depth of the recursive call stack.
#### 2. Iterative
Use an explicit stack to simulate recursion.
```python
def inorderTraversal(root: Optional[TreeNode]) -> List[int]:
    """Iterative."""
    traversal = []
    stack = []
    while root is not None or len(stack) != 0:
        while root is not None:
            stack.append(root)
            root = root.left

        visited = stack.pop()
        traversal.append(visited.val)
        root = visited.right

    return traversal
```
* Time complexity: $O(n)$
	* Visit each node once.
*  Space complexity: $O(n)$
	* Store the result of traversal.
	* Use an explicit stack to track the order.
### References
* [What does [1,null,2,3] mean in binary tree representation?](https://support.leetcode.com/hc/en-us/articles/360011883654-What-does-1-null-2-3-mean-in-binary-tree-representation)
* [Why is the time complexity of Python's list.append() method O(1)?](https://stackoverflow.com/questions/33044883/why-is-the-time-complexity-of-pythons-list-append-method-o1)
* [Difference in complexity of append and concatenate for this list code?](https://stackoverflow.com/questions/33191470/difference-in-complexity-of-append-and-concatenate-for-this-list-code)