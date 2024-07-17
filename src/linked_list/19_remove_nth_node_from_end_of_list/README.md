# [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
> Remove the $n$-th node from the end of a linked list. 
### Input
* `head`: Head node of a linked list.
	* The number of nodes in the list is `sz`.
	* `1 <= sz <= 30`
	* `0 <= Node.val <= 100`
* `n`: Position of a node to be removed.
	* `1 <= n <= sz`
### Output
The head node of the linked list with the specified node being removed.
### Example
```
Input: head = [1, 2, 3, 4, 5], n = 2
Output: [1, 2, 3, 5]
```
### Idea
#### 1. Reverse
To use $n$ in a more intuitive way, we can make the `n`-th node from the end accessible in a forward view. The process is summarized as follows,
1. Reverse the linked list.
2. Point the `next` pointer of `n-1`-th node to `n+1`-th node.
3. Reverse the linked list again and return the original `head`.
```python
# Ignore
```
* Time complexity: $O(n)$
	* All of the 3 steps can be done in $O(n)$.
* Space complexity: $O(1)$
#### 2. Derive Length - Two Pass
Instead of reversing, the length of the linked list is an useful information to position the specified node. The two-pass process is summarized as follows,
1. Derive length of the linked list, so the node to be removed is located.
2. Remove the specified node by repointing the `next` pointer of `n+1` node to `n-1` node.
```python
# Ignore
```
* Time complexity: $O(n)$
	* Each traversal takes $O(n)$.
* Space complexity: $O(1)$
#### 3. Derive Length - One Pass
To avoid traversing the linked list twice, we can use slow and fast pointers to help determine which half the `n`-th node is located in. The concept is summarized as follows,
1. Use slow and fast pointers to derive the middle point.
2. Derive the length of the linked list.
3. Initialize the pointer based on the location of the node to remove.
	*  Either start from the first or the second half.
4. Remove the `n`-th node.

This process looks quite annoying, let's discuss another solution in idea 4.
```python
def removeNthFromEnd(head: Optional[ListNode], n: int) -> Optional[ListNode]:
    if head is None or head.next is None:
        return None

    dummy = ListNode(0, next=head)
    slow, fast = dummy, dummy.next
    mid = 0
    while fast is not None and fast.next is not None:
        mid += 1
        slow = slow.next
        fast = fast.next.next

    ll_len = (mid + 1) * 2 - 1 if fast is None else (mid + 1) * 2
    n_forward = ll_len - n
    p = dummy if n_forward <= mid else slow  # Start from the first or second half
    n_steps = n_forward - 1 if p == dummy else n_forward - mid - 1
    cnt = 0
    while cnt < n_steps:
        cnt += 1
        p = p.next
    p.next = p.next.next

    return dummy.next
```
* Time complexity: $O(n)$
	* Locating the middle point then traversing only a half of the linked list takes $O(n)$.
* Space complexity: $O(1)$
#### 4. One Pass
Removing the `n`-th node is actually repointing the `next` pointer of the `n+1`-th node to `n-1`-th. Also, the gap between the `n+1`-th node and the tail `None` is exactly equal to `n`. With these properties, the one-pass solution can be summarized as follows,
1. Create two pointers `left` and `right` with a gap `n`. 
2. Move `left` and `right` forward until `right` is equal to `None`.
	* `left` now locates the `n+1`-th node.
3. Remove the `n`-th node.

In addition, a dummy node is added to handle the edge case in which the first node is removed. Note that, we can remove only the first node if the linked list has only one node.
```python
def removeNthFromEnd(head: Optional[ListNode], n: int) -> Optional[ListNode]:
    dummy = ListNode(0, next=head)
    l, r = dummy, head
    gap = 0
    while gap < n:
        r = r.next
        gap += 1

    while r is not None:
        l = l.next
        r = r.next 
    l.next = l.next.next

    return dummy.next 
```
* Time complexity: $O(n)$
	* Visiting each node once takes $O(n)$.
* Space complexity: $O(1)$
##### Comment
* This solution can also be implemented without the dummy node. Note that one edge case in which **the first node** is removed should be handled.
```python
def removeNthFromEnd(head: Optional[ListNode], n: int) -> Optional[ListNode]:
    l, r = head, head.next
    gap = 0
    while gap < n and r is not None:
        r = r.next
        gap += 1
    
    if r is None and gap < n:
        # Remove the first node
        return head.next

    while r is not None:
        l = l.next
        r = r.next 
    l.next = l.next.next

    return head 
```