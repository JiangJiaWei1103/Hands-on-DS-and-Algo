# [Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)
> Populate each next pointer to point its next right node in a **perfect** binary tree.
### Input
* `root`: Root of a perfect binary tree.
### Output
Root of a binary tree with its all `next` pointers populated.
### Example
```
Input: root = [1, 2, 3, 4, 5, 6, 7]
Output: [1, #, 2, 3, #, 4, 5, 6, 7, #]
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```
### Idea
#### 1. BFS with Queue
Use level-order traversal to complete population one level at a time. The concept is summarized as follows,
1. It's intuitive to populate `next` **from right to left**, because `next_node` can be tracked easily.
2. For each **outer** loop, the population for **one level** is completed. Let's focus on one visited node,
	1. Pop `visited`.
	2. Populate `visited`'s next to `next_node` (set to `None` at start of each level, indicating the **rightmost**).
	3. Push its left and right children into the queue.
```python
def connect(root: 'Optional[Node]') -> 'Optional[Node]':
    if root is None:
        return None

    q = deque([root])
    while len(q) > 0:
        n_nodes_to_pop = len(q)
        next_node = None
        for _ in range(n_nodes_to_pop):
            visited = q.popleft()
            visited.next = next_node
            next_node = visited
            
            if visited.right is not None:
                q.append(visited.right)
            if visited.left is not None:
                q.append(visited.left)
    
    return root
```
* Time complexity: $O(n)$
	* Traverse a binary tree once.
* Space complexity: $O(n)$
	* Use a queue to store level-wise node set, and the **last** level takes $\frac{n}{2}$.
		* Note that it's **perfect**.
##### Comment
* The rightmost `next` population can be ignored, because its `next` originally points to `None`.
#### 2. BFS with Two Pointers
[![116.png](https://i.postimg.cc/d3FsqGSp/116.png)](https://postimg.cc/8JyQ4fGm)
Use two pointers, one for **level tracking** (green in the figure) and the other for **populating** (red in the figure).
1. `level_tracke` makes sure that population can be safely move down to the next level.
2. `populator` is responsible for populating `next` for nodes at the next level. There are two conditions,
	1. The most intuitive one is to populate `next` of left child to point to right child.
	2. If `next` of `populator` is not `None`, populate `next` of right child to point to left child of `populator.next` (see the second subplot of the figure above). 
```python
def connect(root: 'Optional[Node]') -> 'Optional[Node]':
    if root is None:
        return None

    level_tracker, populator = root, None
    while level_tracker.left is not None:
        populator = level_tracker
        while populator is not None:
            populator.left.next = populator.right
            if populator.next is not None:
                populator.right.next = populator.next.left
            populator = populator.next
        level_tracker = level_tracker.left

    return root
```
* Time complexity: $O(n)$
	* Traverse a binary tree once.
* Space complexity: $O(1)$
#### 3. DFS with Resursion
Keep populating `next` using DFS. The process is summarized as follows,
1. If the node to be populated is `None`, the **base case** is triggered. Hence, no population is needed.
2. Populate `next` of the current node `l` to `r`, and go to the next level. There are to conditions,
	1. `l` is its parent's left child and `r` is right one.
	2. `l` is its parent's right child and `r` is either the left child of its parent's `next` (sibling) or `None` (the **rightmost** case).
```python
def connect(root: 'Optional[Node]') -> 'Optional[Node]':
    def _populate(l: Node, r: Optional[Node]) -> None:
        if l is None:
            return None

        l.next = r
        _populate(l.left, l.right)
        if l.next is not None:
            _populate(l.right, l.next.left)
        else:
            _populate(l.right, None)

    _populate(root, None)
    return root
```
* Time complexity: $O(n)$
	* Traverse a binary tree once.
* Space complexity: $O(h)$, where $h$ is the height of a perfect binary tree
	* The description says that the implicit stack space doesn't count as extra space.
##### Comment
* Can you use recursion to solve the problem w/o modifying the function prototype?