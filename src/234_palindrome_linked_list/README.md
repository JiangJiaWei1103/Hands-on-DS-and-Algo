# [234. Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)
> Determine if the linked list is a **palindrome**, which is a sequence reading the same forward and backward.
### Input
* `head`: Head node of the linked list.
### Output
`True` if the linked list is a palindrome else `False`.
### Example
```
Input: head = [1, 2, 2, 1]
Output: true
```
### Idea
#### 1. Comparison of Whole List
Add all nodes into `list` and compare the reversed counterpart.
```python
def isPalindrome(head: Optional[ListNode]) -> bool:
    if head.next is None:
        return True

    nodes = []
    p = head
    while p is not None:
        nodes.append(p.val)
        p = p.next

    return nodes == nodes[::-1]
```
* Time complexity: $O(n)$
	* Traverse the linked list twice, one for recording and one for comparison.
*  Space complexity: $O(n)$
	* Use an additional list to record the all nodes.
#### 2. Comparison of Two Halves
Find the middle point of the linked list and compare the first and second half.
```python
def isPalindrome(head: Optional[ListNode]) -> bool:
    if head.next is None:
        return True

    # Derive length of the linked list
    ll_len = 0
    p = head
    while p is not None:
        ll_len += 1
        p = p.next
    odd = True if ll_len % 2 == 1 else False
    
    mid_pt = ll_len // 2
    first_half = []
    p = head
    c, mirror_ptr = 0, -1
    while p is not None:
        if c < mid_pt:
            first_half.append(p.val)
        else:
            if not odd or c != mid_pt:
                mirror = first_half[mirror_ptr]
                if mirror != p.val:
                    return False
                mirror_ptr -= 1
        p = p.next
        c += 1
    
    return True
```
* Time complexity: $O(n)$
	* Traverse the linked list twice.
*  Space complexity: $O(n)$
	* Use an additional list to record the first half only, which is 2x memory-efficient than the first idea.
#### 3. Two Pointers w/o Extra Storage
Reverse the second half and compare from both end sides (*i.e.,* head and tail).
```python
def isPalindrome(head: Optional[ListNode]) -> bool:
    # Find the middle point
    slow = fast = head
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse the second half
    prev, slow, prev.next = slow, slow.next, None
    while slow is not None:
        slow.next, prev, slow = prev, slow, slow.next
    
    # Compare two halves
    p, q = head, prev  # Head and tail
    while q is not None:
        if p.val != q.val:
            return False
        p = p.next
        q = q.next

    return True 
```
* Time complexity: $O(n)$
	* Traverse the linked list twice.
* Space complexity: $O(1)$
	* No extra object is used to record node information.
* For three-element swap, the rhs is evaluated first (*i.e.,* a tuple of three elements on the right hand side is created in memory). Then, the tuple is assigned to the lhs. Hence, the final assignment `slow = slow.next` isn't affected by the first `slow.next = prev`.
### References
* [Is there a standardized method to swap two variables in Python?](https://stackoverflow.com/questions/14836228/is-there-a-standardized-method-to-swap-two-variables-in-python)
