# [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)
> Add two non-negative integers represented by two linked lists in reverse order.
### Input
* `l1`: Head node of the first linked list.
* `l2`: Head node of the second linked list.
### Output
Head node of the linked list representing the sum of two integers.
### Example
```
Input: l1 = [2, 4, 3], l2 = [5, 6, 4]
Output: [7, 0, 8]
Explanation: 342 + 465 = 807.
```
### Idea
#### 1. Two Pointers with Dummy Head and Zero
Adding two numbers in reverse order is quite straightforward, because the pointers can move from the least significant digit to the most. Two dummy nodes are added to avoid handling edge cases,
* A **Dummy zero node** `ListNode(0)` pointing to itself is added to avoid handling `None` at tail when one linked list is traversed.
* A **dummy head** `sum_head` is added to avoid handling the start condition.
Then for each digit,
1. Add values from two linked lists and carry.
2. Create a new node with remainder as `val`.
3. Record carry for the next digit.
4. Stop when two linked lists are traversed and no carry is left.
```python
def addTwoNumbers(l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
    # Dummy zero to avoid handling None at tail
    dummy = ListNode(0)
    dummy.next = dummy

    carry = 0
    sum_head = tail = ListNode(-1)
    while l1 != dummy or l2 != dummy or carry == 1:
        cur_sum = l1.val + l2.val + carry
        remainder, carry = cur_sum % 10, cur_sum // 10
        tail.next = ListNode(remainder)
        tail = tail.next

        l1 = l1.next if l1.next is not None else dummy
        l2 = l2.next if l2.next is not None else dummy

    return sum_head.next
```
* Time complexity: $O(max(n, m))$, where $n$ is length of the first linked list and $m$ for the second
	* Traverse two linked lists once.
* Space complexity: $O(n)$
	* Store the sum.
#### 2. Two Pointers w/o Dummy Zero and Carry
The concept is similar to idea 1.
```python
def addTwoNumbers(l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
    cur_sum = 0
    sum_head = tail = ListNode(-1)
    while l1 is not None or l2 is not None:
        cur_sum /= 10
        if l1 is not None:
            cur_sum += l1.val
            l1 = l1.next
        if l2 is not None:
            cur_sum += l2.val
            l2 = l2.next

        tail.next = ListNode(cur_sum % 10)
        tail = tail.next

    # Handle the final carry
    if cur_sum // 10 == 1:
        tail.next = ListNode(1)

    return sum_head.next
```
* Time complexity: $O(max(n, m))$, where $n$ is length of the first linked list and $m$ for the second
	* Traverse two linked lists once.
* Space complexity: $O(n)$
	* Store the sum.
##### Comment
* How to solve this question if linked lists are provided in the forward direction, from the most significant to the least?
	* Reverse linked lists.
	* Use stack.