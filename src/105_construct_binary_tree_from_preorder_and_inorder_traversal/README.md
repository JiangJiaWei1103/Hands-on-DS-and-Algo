# [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
> Construct a binary tree based on it preorder and inorder traversals.
### Input
* `preorder`: Preorder traversal of a binary tree.
* `inorder`: Inorder traversal of a binary tree.
### Output
The constructed binary tree.
### Example
```
Input: preorder = [3, 9, 20, 15, 7], inorder = [9, 3, 15, 20, 7]
Output: [3, 9, 20, null, null, 15, 7]
```
### Idea
Note that this question is related to [108. Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/).
#### 1. Recursive
Recursively divide the tree into root + left subtree + right subtree. For each subtree, it can again  be divided into these three components. The key ideas are,
1. `preorder[0]` must be the root of the target binary tree.
2. `inorder` traversal helps us to locate the dividing point of left and right subtree. Because all values are unique, the index `in_mid` indicating the index of root node in inorder traversal can be derived.
3. Nodes on the left side of `in_mid` form the left subtree, the others on right form the right subtree.
```python
def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    def _build_root(
        in_start: int,
        in_end: int, 
        pre_cur: int
    ) -> Optional[TreeNode]:
        if in_start > in_end:
            return None

        # Similar to build BST from a sorted array
        in_mid = _find_in_mid(inorder[in_start:in_end+1], preorder[pre_cur], base_idx=in_start)
        root = TreeNode(inorder[in_mid])
        # Or, you can build the root first, and find the point to divide left/right
        # root = TreeNode(preorder[pre_cur])
        # in_mid = _find_in_mid(inorder[in_start:in_end+1], preorder[pre_cur], base_idx=in_start) 

        # Build left and right subtrees
        root.left = _build_root(in_start, in_mid-1, pre_cur+1)
        n_nodes_on_left = in_mid - in_start  # Number of nodes on the left subtree (skip them)
        root.right = _build_root(in_mid+1, in_end, pre_cur+n_nodes_on_left+1)

        return root

    def _find_in_mid(sub_inorder: List[int], mid_val: int, base_idx: int) -> int:
        """Find and return the index of root in inorder subarray."""
        for i, val in enumerate(sub_inorder):
            if val == mid_val:
                return base_idx + i
    
    return _build_root(0, len(preorder)- 1, 0)


def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    """Without modifying the function prototype."""
    if len(preorder) == 0:
        return None
    
    root_val = preorder[0]
    root = TreeNode(root_val)
    root_idx = inorder.index(root_val)

    root.left = buildTree(preorder[1:root_idx+1], inorder[:root_idx])
    root.right = buildTree(preorder[root_idx+1:], inorder[root_idx+1:])

    return root
```
* Time complexity: $O(n^2)$
	* Each node is visited once.
	* A linear search is applied for each current root to locate the index in inorder list.
	* List slicing also takes $O(n)$.
* Space complexity: $O(h)$, where $h$ is the height of a BT
	* The depth of call stack is equal to the height of a BT.
#### 2. Recursive w/ Hash Map
Instead of finding the root index in inorder subarray for every `_build_root` call, a hash map from node value to its corresponding index in inorder traversal can be stored in advance and retrieved on the fly.
```python
def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    def _build_root(
        in_start: int,
        in_end: int, 
        pre_cur: int
    ) -> Optional[TreeNode]:
        if in_start > in_end:
            return None
        
        in_mid = val2idx(preorder[pre_cur])  # Retrieve the middle root index on the fly
        root = TreeNode(inorder[in_mid])
        
        # Build left and right subtrees
        root.left = _build_root(in_start, in_mid-1, pre_cur+1)
        n_nodes_on_left = in_mid - in_start
        root.right = _build_root(in_mid+1, in_end, pre_cur+n_nodes_on_left+1)

        return root

    val2idx = {}
    for i, val in enumerate(inorder):
        val2idx[val] = i
    return _build_root(0, len(preorder)- 1, 0)
```
 * Time complexity: $O(n)$
	* Each node is visited once.
	* Creating the hash map takes $O(n)$.
* Space complexity: $O(n)$
	* The hash map takes $O(n)$.
	* The depth of call stack is equal to the height of a BT.
##### Comment
* Can the problem be solved in $O(n)$ w/o a hash map?