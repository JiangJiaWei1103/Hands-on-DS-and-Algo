# [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
> Derive the length of the diameter of a binary tree.
* The **diameter** of a binary tree is the **length** of the **longest path** between any two nodes in a tree. This path may or may not pass through the `root`.
* The **length** of a path between two nodes is represented by the number of edges between them.
### Input
* `root`: Root of a binary tree.
	* The number of nodes in the tree is in the range `[1, 10^4]`.
	* `-100 <= Node.val <= 100`
### Output
The length of the diameter of the tree.
### Example
```
Input: root = [1, 2, 3, 4, 5]
Output: 3
Explanation: 3 is the length of the path [4,2,1,3] or [5,2,1,3].
```
### Idea
Note that the diameter of a root can be defined as follows,
```
diameter = height of left child + height of right child
```
#### 1. Brute-Force
The most intuitive solution is to derive the diameter of each node and keep updating the global max diameter.<br>
In this implementation, we use BFS to traverse the binary tree and derive the diameter of each node.
```python
def diameterOfBinaryTree(root: Optional[TreeNode]) -> int:
    def _cal_hei(root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        return 1 + max(_cal_hei(root.left), _cal_hei(root.right))

    if root is None:
        return 0
    
    d = 0
    q = deque([root])
    while len(q) > 0:
        visited = q.popleft()
        d = max(d, _cal_hei(visited.left) + _cal_hei(visited.right))

        if visited.left is not None:
            q.append(visited.left)
        if visited.right is not None:
            q.append(visited.right)

    return d
```
* Time complexity: $O(n^2)$
	* Deriving the diameter (which takes $O(n)$ itself) for each node in tree takes $O(n^2)$ in total.
* Space complexity: $O(n)$
	* Deriving the height of a single node takes $O(n)$.
		* Think about two extreme cases, perfect and skewed.
##### Comment
* We can also use a nested recursion to solve this problem, which can be counterintuitive.
Deriving the longest path of a single node is quite straightforward, we can formulate the relationship as follows (just as the diameter above),
```
longest_path(root) = max_depth(root.left) + max_depth(root.right)
```
To track the global longest path, we define,
```
max_len_so_far = max(
	longest_path(root), 
	max_len_so_far(root.left),
	max_len_so_far(root.right),
)
```

[![543.png](https://i.postimg.cc/CMXj5vPq/543.png)](https://postimg.cc/z3wLPk1z)

```python
def diameterOfBinaryTree(root: Optional[TreeNode]) -> int:
    def _get_longest_path(root: Optional[TreeNode]) -> int:
        def _get_depth(root: Optional[TreeNode]) -> int:
            if root is None:
                return 0
            
            return 1 + max(_get_depth(root.left), _get_depth(root.right))

        if root is None:
            return 0

        return _get_depth(root.left) + _get_depth(root.right)

    if root is None:
        return 0

    return max(
        _get_longest_path(root),
        diameterOfBinaryTree(root.left),
        diameterOfBinaryTree(root.right),
    )
```
* Time complexity: $O(n^2)$
	* Deriving the diameter (which takes $O(n)$ itself) for each node in tree takes $O(n^2)$ in total.
* Space complexity: $O(n)$
	* Deriving the height of a single node takes $O(n)$.
		* Think about two extreme cases, perfect and skewed.
#### 2. Optimized Recursion
Analyzing the brute-force solution, we can find that there exist many duplicated computations. We know that the diameter is dependent on the height of the node (from the relationship defined in the beginning). Hence, we can update the diameter on the fly when we calculate the height of each node, which can help avoid repeatedly calculating the height.<br>
Simply speaking, the key lies in the relationship of the height and the diameter, in which the diameter can be viewed as a function of the height, `diameter = f(height)`.
```python
def diameterOfBinaryTree(root: Optional[TreeNode]) -> int:
    def _dfs(root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        lhei = _dfs(root.left)
        rhei = _dfs(root.right)
        d[0] = max(d[0], lhei + rhei)

        return 1 + max(lhei, rhei)

    d = [0]
    _dfs(root)

    return d[0]
```
* Time complexity: $O(n)$
	* Visiting each node in the binary tree takes $O(n)$.
* Space complexity: 𝑂(ℎ), where ℎ is the height of the binary tree.
    - The worst case happens when the binary tree is extremely unbalanced, which has a call stack of depth 𝑛. Hence, it takes 𝑂(𝑛) space complexity.
#### Discussion
* It's better to formulate this problem using the concept of **height**, instead of depth, because we can see that DFS keeps updating the length of the longest path and returning (popping out) the the current height of the subtree **from leaf to root**.
	* See [here](https://leetcode.com/problems/diameter-of-binary-tree/solutions/101145/simple-python/comments/591034).