# [103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
> Return the zigzag level order traversal of a binary tree.
* A zigzag level order traversal traverses from left to right, then right to left when going down level by level.
### Input
* `root`: Root of a binary tree.
### Output
The zigzag level order traversal of a binary tree.
### Example
```
Input: root = [3, 9, 20, null, null, 15, 7]
Output: [[3], [20, 9], [15, 7]]
```
### Idea
#### 1. BFS w/ Odd-Level Reversion
Apply normal BFS and reverse the level traversal result at odd level.
```python
def zigzagLevelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    if root is None: return []

    traversal = []
    lv = 0
    q = deque([root])
    while len(q) != 0:
        q_size = len(q)  # Number of nodes at the current level
        lv_traversal = []
        for _ in range(q_size):
            visited = q.popleft()
            lv_traversal.append(visited.val)

            # Push left and right childs of the visited node
            if visited.left is not None:
                q.append(visited.left)
            if visited.right is not None:
                q.append(visited.right)

        if lv % 2 == 1:
            # At odd level (0-index)
            lv_traversal = lv_traversal[::-1]
        traversal.append(lv_traversal)

    return traversal
```
* Time complexity: $O(n)$
	* Each node is visited once.
	* Reversion is done **level-by-level** at only odd levels.
* Space complexity: $O(n)$
	* Store the result of traversal.
	* If the binary tree is perfect, the maximum queue size is bounded by $O(n)$.
##### Comment
* Note that solutions using the concept of reversion isn't a zigzag traversal. Nodes are still traversed in the order of the normal level-order traversal.
#### 2. Double Stack (Ping-Pong)
Use the LIFO property of stack and traverse two levels (one even, one odd) for each outer loop. Because there exist two directions depending on which level the traversal is currently taken, the ordering to push elements into two stacks are different,
1. When traversing the **even** level, elements should be pushed into `odd_stack` **from leftmost to rightmost**, because the **rightmost** should be visited earlier.
2. When traversing the **odd** level, elements should be pushed into `odd_stack` **from rightmost to leftmost**, because the **leftmost** should be visited earlier.
```python
def zigzagLevelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    if root is None:
        return []

    even_stack = [root]
    odd_stack = []
    traversal = []
    while len(even_stack) != 0:
        lv_traversal = []
        while len(even_stack) != 0:
            visited = even_stack.pop()
            lv_traversal.append(visited.val)
            if visited.left is not None:
                odd_stack.append(visited.left)
            if visited.right is not None:
                odd_stack.append(visited.right)
        traversal.append(lv_traversal)
        
        lv_traversal = []
        while len(odd_stack) != 0:
            visited = odd_stack.pop()
            lv_traversal.append(visited.val)
            if visited.right is not None:
                even_stack.append(visited.right)
            if visited.left is not None:
                even_stack.append(visited.left)
        if len(lv_traversal) > 0:
            traversal.append(lv_traversal)

    return traversal
```
* Time complexity: $O(n)$
	* Each node is visited once.
* Space complexity: $O(n)$
	* Store the result of traversal.
	* If the binary tree is perfect, the maximum stack size (can be either one) is bounded by $O(n)$.