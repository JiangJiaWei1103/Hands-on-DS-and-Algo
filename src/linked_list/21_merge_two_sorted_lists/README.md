# [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
> Splice two linked lists sorted in **non-decreasing** order.
### Input
* `list1`: Head node of the first linked list.
* `list2`: Head node of the second linked list.
	* The number of nodes in both lists is in the range `[0, 50]`.
	* -100 <= Node.val <= 100`
	- Both `list1` and `list2` are sorted in **non-decreasing** order.
### Output
The head node of the merged (spliced) linked list. 
### Example
```
Input: list1 = [1, 2, 4], list2 = [1, 3, 4]
Output: [1, 1, 2, 3, 4, 4]
```
### Idea
#### 1. Iterative Sawtooth-Like Comparison
It's intuitive to think of the splicing process as a series of comparisons between node pairs from two lists. Note that after one list is traversed completely, we just need to splice **the remaining sublist** of the other one, because the remaining values of the latter are all greater than the maximum value of the former.<br>
Note that adding a dummy head can help avoid additional conditional branching for the very start.
```python
def mergeTwoLists(list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
    if list1 is None or list2 is None:
        return list2 if list1 is None else list1

    head = ListNode(0)  # Dummy
    r = head
    p, q = list1, list2
    while p is not None and q is not None:
        if p.val <= q.val:
            r.next = p
            p = p.next
        else:
            r.next = q
            q = q.next
        r = r.next

    # Splice the remaining sublist 
    r.next = p if p is not None else q

    return head.next
```
* Time complexity: $O(n+m)$, where $n$ is the length of the first list and $m$ is the second's.
	* Each node in each list is traversed once.
*  Space complexity: $O(1)$
	* No additional objects are created, except for one dummy head node.
##### Comment
* The first two lines are used to handle the edge cases.
* To reduce space complexity to $O(1)$, use `r.next = p` or `r.next = q` instead of creating new nodes (*i.e.,* initialize new objects).
* Can we solve the question without the third pointer `r`?
	* See [here](https://leetcode.com/problems/merge-two-sorted-lists/solutions/1826693/python3-merging-explained/comments/1297564).
```python
```
#### 2. Recursive Sawtooth-Like Comparison
```python
```
* Time complexity: $O(n+m)$
*  Space complexity: $O(n+m)$