# [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)
> Reverse a linked list.
### Input
* `head`: Head node of a linked list.
### Output
Head node of the reversed linked list.
### Example
```
Input: head = [1, 2, 3, 4, 5]
Output: [5, 4, 3, 2, 1]
```
### Idea
#### 1. Iterative
Continuously reverse the `next` pointer when moving forward.
[![206-reverse-linked-list-drawio.png](https://i.postimg.cc/tRBC3RJB/206-reverse-linked-list-drawio.png)](https://postimg.cc/w3spHphN)
```python
def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    if head is None:  # (Optional) 
        return None
        
    prev, cur = None, head
    while cur is not None:
        cur.next, prev, cur = prev, cur, cur.next

    return prev
```
* Time complexity: $O(n)$
	* Traverse a linked list once.
* Space complexity: $O(1)$
#### 2. Recursive
Continuously reverse the `next` pointer within call stack.
* [ ] Add plots of two types of call stacks.
```python
def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    """Function prototype is modified."""
    def _reverse(
        cur: Optional[ListNode],
        prev: Optional[List] = None
    ) -> ListNode:
        if cur is None:
            return prev

        nex = cur.next
        cur.next = prev

        return _reverse(nex, cur)

    return _reverse(head)


def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    """Function prototype is unchanged."""
    if head is None or head.next is None:
        return head

    cur = reverseList(head.next)
    head.next.next = head
    head.next = None

    return cur
```
* Time complexity: $O(n)$
	* Traverse a linked list once.
* Space complexity: $O(n)$
	* Store node objects in the call stack with depth $n$.