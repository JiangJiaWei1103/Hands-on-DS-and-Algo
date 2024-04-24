# [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)
> Find the intersected node of two linked lists. That is, find a node where two linked lists start **merging** with each other.
### Input
* `headA`: Head node of the first linked list.
* `headB`: Head node of the second linked list.
### Output
Intersected node of two linked list if exists else `None`.

### Idea
#### 1. Brute Force
Use a nested loop to compare all the node pairs of two linked lists.
```python
def getIntersectionNode(headA: ListNode, headB: ListNode) -> Optional[ListNode]:
    p = headA
    while p is not None:
        q = headB
        while q is not None:
            if p == q:
                return p
            q = q.next
        p = p.next

    return None
```
* Time complexity: $O(mn)$, where $m$ is the length of the first list and $n$ is the second
	* Traverse all possible node pairs.
* Space complexity: $O(1)$
#### 2. Hash Map
Record node occurrences in the first list, then search the intersection with the second one.
```python
def getIntersectionNode(headA: ListNode, headB: ListNode) -> Optional[ListNode]:
    node_in_a = {}
    while headA is not None:
        node_in_a[headA] = True
        headA = headA.next

    while headB is not None:
        if headB in node_in_a:
            return headB
        headB = headB.next

    return None
```
 * Time complexity: $O(m+n)$, where $m$ is the length of the first list and $n$ is the second
	* Traverse two lists once separately.
* Space complexity: $O(m)$
	* Record node occurrences in the first list.
#### 3. Traversal Alignment
Splice two linked list to align traversal.
```python
def getIntersectionNode(headA: ListNode, headB: ListNode) -> Optional[ListNode]:
    p, q = headA, headB
    while p is not None or q is not None:
        if p == q: return p

        p = headB if p is None else p.next
        q = headA if q is None else q.next

    return None


def getIntersectionNode(headA: ListNode, headB: ListNode) -> Optional[ListNode]:
    p, q = headA, headB
    while p != q:
        p = headB if p is None else p.next
        q = headA if q is None else q.next

    return p 
```
* Time complexity: $O(m+n)$, where $m$ is the length of the first list and $n$ is the second
	* Each pointer traverses a spliced linked list with length $m+n$.
* Space complexity: $O(1)$
##### Comment
* Alignment can be achieved by deriving **length difference** in the beginning.