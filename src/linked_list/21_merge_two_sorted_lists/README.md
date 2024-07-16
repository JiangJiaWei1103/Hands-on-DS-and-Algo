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
    if list1 is None:
        return list2
    elif list2 is None:
        return list1

    head = ListNode(0)  # Dummy head
    p = head 
    while list1 is not None and list2 is not None:
        if list1.val <= list2.val:
            p.next = ListNode(list1.val)  # or list1
            list1 = list1.next
        else:
            p.next = ListNode(list2.val)  # or list2
            list2 = list2.next

        p = p.next

    # Splice the remaining sublist
    p.next = list1 if list1 is not None else list2
    
    return head.next

```
* Time complexity: $O(n+m)$, where $n$ is the length of the first list and $m$ is the second's.
	* Visiting each node of two lists with a total length of $n + m$ takes $O(n+m)$.
*  Space complexity: $O(1)$
	* No additional objects are created, except for one dummy head node.
##### Comment
* The first branching is used to handle the edge cases.
* To reduce space complexity to $O(1)$, use `p.next = list1` and `p.next = list2` instead of creating new nodes (*i.e.,* initialize new objects).
* Can we solve the question without the third pointer `p`?
	* See [here](https://leetcode.com/problems/merge-two-sorted-lists/solutions/1826693/python3-merging-explained/comments/1297564).
```python
```
#### 2. Recursive Sawtooth-Like Comparison
In addition to the iterative method, we can also use recursion to solve the problem. The implementation modifies the function prototype, which can be seen as a direct translated version from the iterative approach. To maintain the original function prototype, we splice two lists in a **reverse** order. Concretely speaking, we first go as deep as we can, then keep repointing the `next` pointers of `list` and `list2` to the returned **spliced** list.
```python
def mergeTwoLists(list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
    """Recursive with the modified function prototype."""
    def _helper(p: ListNode, list1: Optional[ListNode], list2: Optional[ListNode]) -> None:
        if list1 is None:
            p.next = list2
            return
        elif list2 is None:
            p.next = list1
            return 

        if list1.val <= list2.val:
            p.next = list1
            _helper(p.next, list1.next, list2)
        else:
            p.next = list2
            _helper(p.next, list1, list2.next)

    head = ListNode(0)
    p = head
    _helper(p, list1, list2)
    
    return head.next


def mergeTwoLists(list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
    """Recursive with the original function prototype."""
    if list1 is None:
        return list2
    elif list2 is None:
        return list1

    if list1.val <= list2.val:
        list1.next = mergeTwoLists(list1.next, list2)
        return list1
    else:
        list2.next = mergeTwoLists(list1, list2.next)
        return list2
```
* Time complexity: $O(n+m)$
	* Visiting each node of two lists with a total length of $n + m$ takes $O(n+m)$.
*  Space complexity: $O(n+m)$
	* The depth of call stack takes $O(n+m)$.