# [143. Reorder List](https://leetcode.com/problems/reorder-list/)
> Reorder a linked list as illustrated below. Note that node values can't be changed.
* `L0 → L1 → … → Ln - 1 → Ln` --> `L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …`
	* Interleave nodes in forward and backward orderings.
### Input
* `head`: Head node of a linked list.
	* The number of nodes in the list is in the range `[1, 5 * 10^4]`.
	* `1 <= Node.val <= 1000`
### Output
`None`, nodes should be modify **in-place**.
### Example
```
Input: head = [1, 2, 3, 4, 5]
Output: [1, 5, 2, 4, 3]
```
### Idea
#### 1. Merging Two Halves - Forward and Backward
The reordered linked list is defined as merging nodes **from forward and backward views alternately**. Hence, the problem can be broken down into the following key steps,
1. Find the middle point of the linked list, which can be thought of as putting a mirror in the middle.
2. Reverse the second half of the linked list.
3. Merge the first and the second halves, the former steps forward and the latter steps backward.

[![143.png](https://i.postimg.cc/NG8dkd7r/143.png)](https://postimg.cc/6T3V9LP9)
Note that the length of the first half is always longer or equal to the second's in this implementation, because merging is done by **forward first** (*i.e.,* the first half has higher priority).
```python
def reorderList(head: Optional[ListNode]) -> None:
    """
    Do not return anything, modify head in-place instead.
    """
    # Derive length of the linked list
    t = head
    ll_len = 1
    while t.next is not None:
        t = t.next
        ll_len += 1

    # Reverse the second half
    prev, cur = None, head
    cnt = 0 
    while cnt < ll_len / 2:
        cur = cur.next
        cnt += 1
    while cur is not None:
        cur.next, prev, cur = prev, cur, cur.next

    # Merge two halves
    h = head
    while t is not None:
        h.next, t.next, h, t = t, h.next, h.next, t.next
    h.next = None
```
* Time complexity: $O(n)$
	* Each of the three stages in this solution is done with $O(n)$.
* Space complexity: $O(1)$
##### Comment
* Without explicitly deriving length of the linked list, we can further optimize the solution by using slow and fast pointers for halving.
```python
def reorderList(head: Optional[ListNode]) -> None:
    slow, fast = head, head.next
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next

    prev, cur = None, slow.next
    slow.next = None  # Tail of the first half
    while cur is not None:
        # Can be done with 3-element swap
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt

    h, t = head, prev
    while t is not None:
        # Can be done with 4-element swap
        h_nxt, t_nxt = h.next, t.next
        h.next, t.next = t, h.next
        h, t = h_nxt, t_nxt


def reorderList(head: Optional[ListNode]) -> None:
    """This implementation is off-by-one of the above."""
    slow, fast = head, head
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next

    prev, cur = None, slow
    while cur is not None:
        cur.next, prev, cur = prev, cur, cur.next

    l, r = head, prev
    while l != r and l.next != r:
        l.next, r.next, l, r = r, l.next, l.next, r.next

```
#### 2. Stack
Push all nodes into a stack to create a **reverse view** of the input linked list based on the LIFO property.
* Time complexity: $O(n)$
	* Both creating a stack and reordering take $O(n)$.
* Space complexity: $O(n)$
	* A stack storing the reverse view of the linked list takes $O(n)$.
### Discussion
* Is there any technique to avoid tracking the temporary states of pointers?
	* Using the swapping technique can be a workaround, but the line can become quite lengthy.