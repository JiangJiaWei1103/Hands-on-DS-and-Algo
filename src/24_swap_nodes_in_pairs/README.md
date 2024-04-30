# [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)
> Swap every two adjacent nodes in a linked list.
### Input
* `head`: Head node of a linked list.
### Output
Head node of a swapped linked list.
### Example
```
Input: head = [1, 2, 3, 4]
Output: [2, 1, 4, 3]
```
### Idea
#### 1. Recursive
Recursively modify `next` pointers backward.<br>

[![24-swap-nodes-in-pairs-drawio.png](https://i.postimg.cc/WbtKDc6M/24-swap-nodes-in-pairs-drawio.png)](https://postimg.cc/svdK88LX)
```python
def swapPairs(head: Optional[ListNode]) -> Optional[ListNode]:
    # Base case
    if head is None or head.next is None:
        return head

    new_head = head.next
    head.next = swapPairs(head.next.next)
    new_head.next = head

    return new_head
```
* Time complexity: $O(n)$
	* Traverse the linked list once.
* Space complexity: $O(n)$
	* The depth of call stack is about $\frac{n}{2}$.
#### 2. Iterative
Iteratively modify `next` pointers forward with an auxiliary dummy node for handling head node.
```python
def swapPairs(head: Optional[ListNode]) -> Optional[ListNode]:
    if head is None or head.next is None:
        return head

    dummy = ListNode()
    prev, cur = dummy, head
    while cur is not None and cur.next is not None:
        prev.next = cur.next
        cur.next = prev.next.next
        prev.next.next = cur

        prev = cur
        cur = cur.next
    
    return dummy.next
```
* Time complexity: $O(n)$
	* Traverse the linked list once.
* Space complexity: $O(1)$