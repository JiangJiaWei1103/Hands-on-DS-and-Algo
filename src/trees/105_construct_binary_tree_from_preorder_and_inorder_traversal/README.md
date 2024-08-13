# [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
> Construct a binary tree based on its preorder and inorder traversals.
### Input
* `preorder`: Preorder traversal of a binary tree.
	* `1 <= preorder.length <= 3000`
* `inorder`: Inorder traversal of a binary tree.
	* `inorder.length == preorder.length`
	* `-3000 <= preorder[i], inorder[i] <= 3000`
	* `preorder` and `inorder` consist of **unique** values.
	* Each value of `inorder` also appears in `preorder`.
### Output
Root of the constructed binary tree.
### Example
```
Input: preorder = [3, 9, 20, 15, 7], inorder = [9, 3, 15, 20, 7]
Output: [3, 9, 20, null, null, 15, 7]
```
### Idea
Observing the preorder and inorder traversals, we know that there are two important information sufficient for us to successfully construct the binary tree,
1. `preorder[0]` must be the root.
2. `inorder` tells us which nodes are located on the left and right sides of the root.
#### 1. Recursive - DFS
Based on the core ideas, the intuitive solution is to recursively build the tree by the following process,
1. Build the root with the first node in `preorder`.
2. Build the left subtree with the split left nodes.
3. Build the right subtree with the split right nodes.

Concretely speaking, elements on the left side of the root node in `inorder` belongs to the left subtree, and the others are on the right.
```python
def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    if len(preorder) == 0 and len(inorder) == 0:
        return None

    root = TreeNode(preorder[0])
    in_mid = inorder.index(preorder[0])
    root.left = buildTree(preorder[1:in_mid+1], inorder[:in_mid])
    root.right = buildTree(preorder[in_mid+1:], inorder[in_mid+1:])

    return root
```
* Time complexity: $O(n^2)$
	* Visiting each node in the binary tree takes $O(n)$.
		* Each visit performs a linear search and list slicing, which take $O(n)$.
	* Overall, the nested operation takes $O(n^2)$ in total.
* Space complexity: $O(n^2)$ (?)
	* If the binary tree **skewed**, the depth of call stack is $n$, each of which takes $O(n)$ to store the list copies.
#### 2. Recursive - Linear DFS
To reduce the time complexity from quadratic to linear, we need to optimize the following two aspects,
1. Retrieve the index of root node in `inorder` in $O(1)$.
2. Avoid list slicing.

For the former, we create a hash map mapping from the root value to the index in `inorder`. So, the root index can be retrieved on the fly. As for the latter, we keep track of index boundaries for left and right subtrees.
```python
def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    def _build_tree(
        pre_start: int,
        in_start: int,
        in_end: int,
    ) -> Optional[TreeNode]:
        # Base case
        if in_start > in_end:
            return None
        
        # Core logic
        root = TreeNode(preorder[pre_start])
        in_mid = root_v2i[preorder[pre_start]]
        n_lefts = in_mid - in_start
        root.left = _build_tree(pre_start+1, in_start, in_mid-1)
        root.right = _build_tree(pre_start+n_lefts+1, in_mid+1, in_end)

        return root

    root_v2i= {v: i for i, v in enumerate(inorder)}
    root = _build_tree(0, 0, len(inorder) - 1)

    return root
```
 * Time complexity: $O(n)$
	 * Creating the hash map takes $O(n)$.
	* Visiting each node in the BT takes $O(n)$.
* Space complexity: $O(n)$
	* A hash map storing the root index in `inorder` takes $O(n)$.
	* The worst case of recursion happens when the BT is **skewed**, which has a call stack of depth $n$.
##### Comment
* Diving into idea 2, we can observe an interesting phenomenon. That is, we must skip all left nodes based on the current root to get right nodes in `preorder`. Also, we can make sure that the left subtree is built completely before we start to build the right one. Hence, we now provide another solution without tracking `pre_start`. Because the `preorder` iterator moves forward (i.e., `next`) every time a new node is constructed, we can guarantee all left nodes are skipped (just as what `n_lefts` does in the solution above). However, note that `iter` or `deque` incurs another $O(n)$ to build, which can be optimized by tracking an integer indicating the position in `preorder`.
```python
def buildTree(preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    def _build_tree(
        in_start: int,
        in_end: int,
    ) -> Optional[TreeNode]:
        if in_start > in_end:
            return None
        
        root = TreeNode(next(preorder))
        in_mid = root_v2i[root.val]
        root.left = _build_tree(in_start, in_mid-1)
        root.right = _build_tree(in_mid+1, in_end)

        return root

    preorder = iter(preorder)  # Or deque
    root_v2i= {v: i for i, v in enumerate(inorder)}
    root = _build_tree(0, len(inorder) - 1)

    return root
```
##### Comment
* Can the problem be solved in $O(n)$ w/o a hash map?
	* See [Simple O(n) without map](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solutions/34543/simple-o-n-without-map/) and [Precise one-pass iterative solution O(n)](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solutions/214622/Precise-one-pass-iterative-solution-O(n).
	* [Intuitive iterative solution](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solutions/34555/the-iterative-solution-is-easier-than-you-think/comments/117721).