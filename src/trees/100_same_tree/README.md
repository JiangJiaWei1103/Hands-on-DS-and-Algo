# [100. Same Tree](https://leetcode.com/problems/same-tree/)
> Check if two binary trees are structurally identical and all nodes have the same values.
### Input
* `p`: Root of the first binary tree.
* `q`: Root of the second binary tree.
	* The number of nodes in both trees is in the range `[0, 100]`.
	* `-10^4 <= Node.val <= 10^4`
### Output
`True` if two binary trees are the same else `False`.
### Example
```
Input: p = [1, 2, 3], q = [1, 2, 3]
Output: true
```
### Idea
The idea is quite straightforward. Let `v1` be the visited node in `p` and `v2` in `q`, we consider the following three conditions,
1. `v1 is None or v2 is None`
	* If either of the nodes is `None`, we need to make sure both are `None`.
		* Hence, both `None` can be viewed as a special case.
2. `v1.val == v2.val`
	* If both nodes aren't `None`, the values should be the same.
#### 1. Recursive - DFS
For recursive solutions, we first break down the problem as described above. In the first implementation, we can clearly see how each condition is handled. Next, we can combine conditions to make the implementation neat. We can choose whichever traversal technique in our arsenal (e.g., inorder, preorder, postorder).
```python
def isSameTree(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    if p is None and q is None:
        return True

    if p is None or q is None or p.val != q.val:
        return False

    return (
        isSameTree(p.left, q.left) 
        and isSameTree(p.right, q.right)
    )

def isSameTree(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    def _preorder(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if p is None or q is None:
            return p is None and q is None
        
        return (
            (p.val == q.val)
            and _preorder(p.left, q.left)
            and _preorder(p.right, q.right)
        )

    def _inorder(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if p is None or q is None:
            return p is None and q is None
        
        return (
            _inorder(p.left, q.left)
            and (p.val == q.val)
            and _inorder(p.right, q.right)
        )

    def _postorder(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if p is None or q is None:
            return p is None and q is None
        
        return (
            _inorder(p.left, q.left)
            and _inorder(p.right, q.right)
            and (p.val == q.val)
        )

    return _inorder(p, q)


def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    if p is None or q is None:
        return p is None and q is None

    return (
        self.isSameTree(p.left, q.left)
        & (p.val == q.val)
        & self.isSameTree(p.right, q.right)
    )
```
* Time complexity: $O(|p| + |q|)$, where $|p|$ is the number of nodes in `p` and $|q|$ is the number of nodes in `q`.
	* Visiting each node in both trees takes $O(|p| + |q|)$ (or simply $O(n)$). 
* Space complexity: $O(h)$, where $h$ is the height of the binary tree.
	* The worst case happens when the binary tree is **skewed**, which incurs a call stack with depth $h$.
#### 2. Iterative - BFS
We can also solve this problem through an iterative approach. Here, I choose to implement a **BFS** solution, which is just a level-order traversal.
```python
def isSameTree(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    qu = deque([(p, q)])
    while len(qu) > 0:
        vp, vq = qu.popleft()

        # Run same test
        if vp is None and vq is None:
            continue
        elif vp is None or vq is None or vp.val != vq.val:
            return False
        else:
            qu.append((vp.left, vq.left))
            qu.append((vp.right, vq.right))

    return True
```
* Time complexity: $O(|p| + |q|)$, where $|p|$ is the number of nodes in `p` and $|q|$ is the number of nodes in `q`.
	* Visiting each node in both trees takes $O(|p| + |q|)$ (or simply $O(n)$). 
* Space complexity: $O(n)$
	* A queue storing nodes in both trees level by level takes $O(n)$ in the worst case, in which the binary tree is **perfect**.
#### Discussion
* Note that we can't just do a element-wise value comparison between two trees, because one value traversal can't uniquely define a binary tree. We'll need two value-only tree traversals to define an unique binary tree.
	* [Advanced topic](https://leetcode.com/problems/subtree-of-another-tree/solutions/474425/java-python-2-solutions-naive-serialize-in-preorder-then-kmp-o-m-n-clean-concise/comments/2233064)