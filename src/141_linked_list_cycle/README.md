# [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
> Determine if the linked list has a cycle.
### Input
* `head`: Head node of the linked list.
### Output
`True` if the cycle exists else `False`.
### Idea
#### 1. Linked List Traversal
Traverse linked list and determine the current node has been visited or not.
```python
def hasCycle(head: Optional[ListNode]) -> bool:
	visited = set()
	p = head
	while p is not None:
		if p in visited:
			return True
		visited.add(p)
		p = p.next
	
	return False
```
* Time complexity: $O(n)$
	* Traverse the linked list only once.
	* Python set lookup takes $O(1)$ on average.
* Space complexity: $O(n)$
	* Use another `set` to record the visited nodes.
#### 2. Cycle Detection via Slow-Fast Pointers
Use the concept of **relative velocity** to determine if slow and fast pointers meet at some point.
```python
def hasCycle(head: Optional[ListNode]) -> bool:
	if head is None:
		return False

	walker = head  # Slow
	runner = head  # Fast
	while runner.next is not None and runner.next.next is not None:
		walker = walker.next
		runner = runner.next.next
		if walker == runner:
			return True
	
	return False
```
* Time complexity: $O(n)$
	* If there exists a cycle, can I prove that the walker will be caught up by the runner in the first round?
* Space complexity: $O(1)$
### References
* [Cycle detection](https://en.wikipedia.org/wiki/Cycle_detection)
* [Internal working of Set in Python](https://www.geeksforgeeks.org/internal-working-of-set-in-python/)