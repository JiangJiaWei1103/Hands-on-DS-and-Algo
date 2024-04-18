# [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
> Splice two linked lists sorted in **non-decreasing** order.
### Input
* `list1`: Head node of the first linked list.
* `list2`: Head node of the second linked list.
### Output
The head of the merged (spliced) linked list. 
### Example
```
Input: list1 = [1, 2, 4], list2 = [1, 3, 4]
Output: [1, 1, 2, 3, 4, 4]
```
### Idea
#### 1. Sawtooth-Like Comparison with 3 Pointers
Use two pointers to compare current values of two lists and continually add the smaller one to the merged list, tracked by the other pointer.
```python
def mergeTwoLists(list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
	"""Three-pointer solution w/o dummy node."""
    if list1 is None or list2 is None:
        return list1 if list1 is not None else list2        

    # Both lists have at least one value
    p = None
    p1, p2 = list1, list2
    while p1 is not None and p2 is not None:
        val1, val2 = p1.val, p2.val
        if val1 <= val2:
            new_node = ListNode(val=val1)
            p1 = p1.next
        else:
            new_node = ListNode(val=val2)
            p2 = p2.next

        if p is None:
            p = new_node
            merged_head = p
        else:
            p.next = new_node
            p = p.next

	# Merge the remaining part
    p.next = p1 if p1 is not None else p2

    return merged_head 


def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
	"""Three-pointer solution w/ dummy node."""
	if list1 is None or list2 is None:
		return list1 if list1 is not None else list2
	
	dummy_head = ListNode()
	p = dummy_head
	p1, p2 = list1, list2
	while p1 is not None and p2 is not None:
		val1, val2 = p1.val, p2.val
		if val1 <= val2:
			new_node = ListNode(val=val1)
			p1 = p1.next
		else:
			new_node = ListNode(val=val2)
			p2 = p2.next
	
		p.next = new_node
		p = p.next
	
	# Merge the remaining part 
	p.next = p1 if p1 is not None else p2
	
	return dummy_head.next
```
* Time complexity: $O(n)$ (or $O(n+m)$?)
	* Traverse each list at least once.
		* The remaining part of one list can be skipped.
*  Space complexity: $O(n)$
	* Create a new node for each visited in `while`.
##### Comment
* The first two lines of the solution are used to handle the edge case, which provides some runtime performance boost.
* To reduce space complexity to $O(1)$, use `p.next = p1` or `p.next = p2` instead of creating new nodes for those visited. 
	* For submission, the method using `new_node` always outperforms in terms of runtime.
#### 2. Sawtooth-Like Comparison with 2 Pointers

```python
```
* Time complexity:
	* Explanation
*  Space complexity:
	* Explanation