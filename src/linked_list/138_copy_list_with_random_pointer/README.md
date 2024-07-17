# [138. Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)
> Construct a deep copy of a linked list, in which each node has an additional field `random` which can point to any node or `null`.
### Input
* `head`: Head of a linked list.
	* `0 <= n <= 1000`
	* `-10^4 <= Node.val <= 10^4`
	* `Node.random` is `null` or is pointing to some node in the linked list.
### Output
The head node of the copied linked list.
### Example
```
# Just a deep copy of the given list.
Input: head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
Output: [[7,null],[13,0],[11,4],[10,2],[1,0]]
```
### Idea
#### 1. Two Pass w/  Hash Map
The atomic operation in this problem is to clone a single node in the linked list. What's important is that we need to avoid creating duplicated copies of the same node. A naive solution is to use a hash map to store the mapping from the old node to its copy. We can implement the solution in two ways,
1. Constructing the hash map and build the naive linked list in the first pass, and fill `random` in the second pass.
2. Constructing only the hash map in the first pass, and complete all links in the second pass.

The second implementation is more concise and clear.
```python
def copyRandomList(head: 'Optional[Node]') -> 'Optional[Node]':
    # Construct the hash map and build the linked list with next filled
    o2n = {}
    dummy = Node(0)
    new, old = dummy, head
    while old is not None:
        node = Node(x=old.val)
        new.next = node
        o2n[old] = node

        new = new.next
        old = old.next

    # Fill random
    new, old = dummy.next, head
    while old is not None:
        if old.random is not None:
            new.random = o2n[old.random]

        new = new.next
        old = old.next

    return dummy.next


def copyRandomList(head: 'Optional[Node]') -> 'Optional[Node]':
    # Construct the hash map
    o2n = {None: None}
    old = head
    while old is not None:
        o2n[old] = Node(old.val)
        old = old.next

    # Complete all links
    old = head
    while old is not None:
        new = o2n[old]
        new.next = o2n[old.next]
        new.random = o2n[old.random]
        old = old.next

    return o2n[head]
```
* Time complexity: $O(n)$
	* Both passes through the entire linked list take $O(n)$.
* Space complexity: $O(n)$
	* A hash map storing the mapping from old nodes to cloned ones take $O(n)$.
### Discussion
* Can you solve the problem with constant auxiliary space?
