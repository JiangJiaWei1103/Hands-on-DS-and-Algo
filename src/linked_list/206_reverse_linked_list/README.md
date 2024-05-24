# [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)
> Reverse a linked list.
### Input
* `head`: Head node of a linked list.
	* The number of nodes in the list is the range `[0, 5000]`.
	* `-5000 <= Node.val <= 5000`
### Output
Head node of the reversed linked list.
### Example
```
Input: head = [1, 2, 3, 4, 5]
Output: [5, 4, 3, 2, 1]
```
### Idea
#### 1. Iterative
Reversing a linked list is just a simple process of repointing `next` pointer of each node. The challenge is how we can move forward without losing track. To handle this issue, we use a `nxt` pointer to flag the next position to go, as illustrated below. To be concrete, we need to do the following operations for each iteration,
1. `nxt = cur.next`: Record the next position to go.
2. `cur.next = prev`: Repoint the `next` pointer of current node to the previous one.
3. `prev = cur`: Move `prev` pointer forward.
4. `cur = next`: Move `cur` pointer forward.
Finally, we need to repoint `next` of the original `head` (tail now) to `None`.

[![206-iter-v2.png](https://i.postimg.cc/3w6CP1G7/206-iter-v2.png)](https://postimg.cc/rd1r4SgY)

```python
def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    if head is None:
        return None

    prev, cur = head, head.next
    while cur is not None:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt

    # The original head becomes tail
    head.next = None
    
    return prev
```
* Time complexity: $O(n)$
	* Each node is visited once.
* Space complexity: $O(1)$
##### Comment
* To avoid handling `head` separately, we can initialize `prev, cur = None, head`.
	* We don't need to directly return `None` when `head is None`.
	* We don't need to repoint `head.next` to `None` in the end.
```python
def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    # if head is None:
    #     return None

    prev, cur = None, head
    while cur is not None:
        nxt = cur.next
        cur.next = prev
        prev = cur
        cur = nxt
    
    return prev  
```
* Leveraging the variable swapping technique, we can make the solution more concise. The logic behind the scene is that rhs is evaluated first, so a tuple of variables is created at the first hand.
```python
def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    # if head is None:
    #     return None

    prev, cur = None, head
    while cur is not None:
        cur.next, prev, cur = prev, cur, cur.next

    return prev   
```
#### 2. Recursive
With recursion, we again keep repointing `next` pointers. The challenge now becomes how we can **return head node of the reversed linked list**.

[![206-recur.png](https://i.postimg.cc/c1BSyRfy/206-recur.png)](https://postimg.cc/Lh57jZyN)

```python
def reverseList(head: Optional[ListNode]) -> Optional[ListNode]:
    if head is None or head.next is None:
        return head

    new_head = reverseList(head.next)
    head.next.next = head
    head.next = None

    return new_head 
```
* Time complexity: $O(n)$
	* Each node is visited once.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.