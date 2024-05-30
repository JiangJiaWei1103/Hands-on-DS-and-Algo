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
The idea is quite straightforward. Let `v1` be the visited node in `p` and `v2` in `q`, we consider the following two conditions,
1. `v1.val == v2.val`
	* If both nodes aren't `None`, the values should be the same.
1. `v1 is None or v2 is None`
	* If either of the nodes is `None`, we need to make sure both are `None`.
#### 1. Recursive
Recursive solution is easy to implement, we can choose whichever traversal technique in our arsenal (*e.g.,* inorder, preorder, postorder).
```python
def isSameTree(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    def _preorder(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if p is None or q is None:
            return p is None and q is None
        
        return (
            (p.val == q.val)
            & _preorder(p.left, q.left)
            & _preorder(p.right, q.right)
        )

    def _inorder(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if p is None or q is None:
            return p is None and q is None
        
        return (
            _inorder(p.left, q.left)
            & (p.val == q.val)
            & _inorder(p.right, q.right)
        )

    def _postorder(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        if p is None or q is None:
            return p is None and q is None
        
        return (
            _inorder(p.left, q.left)
            & _inorder(p.right, q.right)
            & (p.val == q.val)
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
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
#### 2. Iterative
We can also solve this problem through an iterative approach. Here, I choose to implement a BFS solution, which is just a level-order traversal.
```python
def isSameTree(p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    qu = deque([(p, q)])
    while len(qu) > 0:
        v1, v2 = qu.popleft()
        if v1 is None and v2 is None:
            continue
        elif v1 is None or v2 is None:
            return False
        
        if v1.val != v2.val:
            return False
        qu.append((v1.left, v2.left))
        qu.append((v1.right, v2.right))

    return True
```
* Time complexity: $O(|p| + |q|)$, where $|p|$ is the number of nodes in `p` and $|q|$ is the number of nodes in `q`.
	* Visiting each node in both trees takes $O(|p| + |q|)$ (or simply $O(n)$). 
* Space complexity: $O(n)$
	* A queue storing nodes in both trees level by level takes $O(n)$.
#### Discussion
* Note that we can't just do a element-wise value comparison between two trees, because one value traversal can't uniquely define a binary tree. We'll need two value-only tree traversals to define an unique binary tree.