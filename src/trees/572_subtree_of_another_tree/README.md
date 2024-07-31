# [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)
> Determine if one binary tree is a subtree of the other.
* A subtree of a binary tree `tree` is a tree that consists of a node in `tree` and all of this node's descendants. The tree `tree` could also be considered as a subtree of itself.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the `root` tree is in the range `[1, 2000]`.
	* `-10^4 <= root.val <= 10^4`
* `subRoot`: Root of a potential subtree of `root`.
	* The number of nodes in the `subRoot` tree is in the range `[1, 1000]`.
	* `-10^4 <= subRoot.val <= 10^4`
### Output
`True` if `subRoot` is a subtree of `root` else `False`.
### Example
```
Input: root = [3, 4, 5, 1, 2], subRoot = [4, 1, 2]
Output: true
```
### Idea
#### 1. Brute-Force
The most intuitive way to solve this problem is to borrow the idea of checking the same tree. We can think of this process as running a nested loop,
1. For outer, each node in tree `root` will act as a root (i.e., the root of the candidate subtree).
2. For inner, match `subRoot` with this specific root to check if two trees are the same.

[![572.png](https://i.postimg.cc/0N0cpdC7/572.png)](https://postimg.cc/R3q7mty0)

Because `subRoot` can be a subtree of `tree` at any part of `tree`, we formulate the checking process as follows,
```
is_subtree = (
	_is_same(root, subRoot)  # Match at the current root
	or isSubtree(root.left, subRoot)  # "Any" match at left
	or isSubtree(root.right, subRoot)  # "Any" match at right
)
```
```python
def isSubtree(root: Optional[TreeNode], subRoot: Optional[TreeNode]) -> bool:
    def _is_same(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if p is None or q is None:
            return p is None and q is None
        
        return (
            (p.val == q.val)
            and _is_same(p.left, q.left)
            and _is_same(p.right, q.right)
        )

    if root is None:
        return subRoot is None

    return  (
        _is_same(root, subRoot)
        or isSubtree(root.left, subRoot)
        or isSubtree(root.right, subRoot)
    )
```
* Time complexity: $O(|s||t|)$ , where $|s|$ is the number of nodes in `root` and $|t|$ in `subRoot`.
	* Running a **nested** check takes quadratic time, $O(|s||t|)$.
* Space complexity: $O(h)$, where $h$ is the height of `root`.
	* In the worst case, a **skewed** binary tree has a call stack of depth $s$.
#### 2. Hashing - Merkle Tree
To alleviate the quadratic time complexity, we use a technique commonly seen in the field of cryptography, called Merkle tree. The concept is to continuously hash each node from leave to root. Because the **hash code** of each node contains the complete information **describing the structure and values of that subtree**, we just need to find if the hash code of `subRoot` exists in `root`.<br>
To be concrete, we use a set to store all hash codes of  `root` and check if the hash code of `subRooot` exists in this set.
```python
def isSubtree(root: Optional[TreeNode], subRoot: Optional[TreeNode]) -> bool:
    def _hashify(root: Optional[TreeNode], add_to_memo: bool = True):
        if root is None:
            return None

        code = (root.val, _hashify(root.left), _hashify(root.right))
        if add_to_memo:
            memo.add(code)

        return code

    memo = set()
    _hashify(root)
    
    return _hashify(subRoot, add_to_memo=False) in memoo
```
* Time complexity: $O(|s|+|t|)$ , where $|s|$ is the number of nodes in `root` and $|t|$ in `subRoot`.
	* Hashing `root` takes $O(|s|)$ and hashing `subRoot` takes $O(|t|)$.
	* Matching hash codes takes $O(1)$. <span style="color: orange">(?)</span>
		* The hash code is a nested tuple object.
* Space complexity: $O(|s|+|t|)$
	* A set storing all hash codes of `root` and `subRoot` takes $O(|s|+|t|)$.
##### Comment
* We add another parameter to decide whether to add the hash code to `memo` to avoid adding the hash code of `subRoot`.
* Also check [this solution](https://leetcode.com/problems/subtree-of-another-tree/solutions/102741/python-straightforward-with-explanation-o-st-and-o-s-t-approaches/), which in-place modifies the value of each node to the hash code and doesn't use `memo`.
#### 3. String Matching - Brute-Force
Convert `root` and `subRoot` to strings and run a string matching algorithm.
```python
```
* Time complexity: $O(|s||t|)$ , where $|s|$ is the number of nodes in `root` and $|t|$ in `subRoot`.
* Space complexity: $O(|s|+|t|)$
##### Comment
* How to make sure that the converted string can **uniquely define** the corresponding binary tree?
#### 4. String Matching - KMP Algorithm
Convert `root` and `subRoot` to strings and run KMP algorithm.

### References
* [Merkle tree](https://en.wikipedia.org/wiki/Merkle_tree)
