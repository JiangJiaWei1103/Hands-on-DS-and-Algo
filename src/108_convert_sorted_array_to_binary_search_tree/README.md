# [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)
> Convert an integer array sorted in **strictly increasing order** to a **height-balanced** BST.
* A **height-balanced** binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.
### Input
* `nums`: An integer array.
### Output
Root of the converted BST.
### Example
```
Input: nums = [-10, -3, 0, 5, 9]
Output: [0, -3, 9, -10, null, 5] or [0, -10, 5, null, -3, null, 9]
```
* Accept multiple implementations.
### Idea
#### 1. Recursive - Mid Point as Root
Treat the **middle point** of each `nums` slice as the current root and recursively build left and right subtrees.
```python
def sortedArrayToBST(nums: List[int]) -> Optional[TreeNode]:
    def _build_root(l: int, r: int) -> Optional[TreeNode]:
        if l > r:
            return None
            
        # Find the current middle point and build a inner root
        mid_pt = (l+r) // 2
        root = TreeNode(nums[mid_pt])

        # Build left and right subtrees
        root.left = _build_root(l, mid_pt-1)
        root.right = _build_root(mid_pt+1, r)

        return root
    
    # Start from the leftmost and rightmost indices
    return _build_root(0, len(nums)-1)
```
* Time complexity: $O(n)$
	* Each number in `nums` is treated as a root once.
*  Space complexity: $O(log\ n)$
	* For a DFS-like building process, the call stack is equal to the height of a **height-balanced** BST.
##### Comment
* If list slicing is used, instead of passing lower and upper bounds, the time complexity will grow to $O(n\ log\ n)$. See [here](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/solutions/35223/an-easy-python-solution/comments/319432) for explanation.
#### 2. Iterative
* [ ] Implement idea 1 in iterative manner.