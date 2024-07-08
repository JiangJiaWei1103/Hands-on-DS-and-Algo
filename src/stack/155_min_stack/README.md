# [155. Min Stack](https://leetcode.com/problems/min-stack/)
> Design a stack that supports push, pop, top, and retrieving the minimum element in **constant** time.
### Input
* `val` (used in `obj.push(val)`): A value to push into the stack.
	* `-2^31 <= val <= 2^31 - 1`
* Methods `pop`, `top` and `getMin` operations will always be called on **non-empty** stacks.
* At most `3 * 10^4` calls will be made to `push`, `pop`, `top`, and `getMin`.
### Output
### Example
```
Input
["MinStack", "push", "push", "push", "getMin", "pop", "top", "getMin"]
[[], [-2], [0], [-3], [], [], [], []]

Output
[null, null, null, null, -3, null, 0, -2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
```
### Idea
One of the requirements is that all operations should be completed in **constant** time. `getMin()`, in particular, is the most troublesome. Using a single value `self.min_val` will lose track of the  minimum value if the current min is popped out because we don't record the next min for a sequence of `push` and `pop` operations.
#### 1. Min Heap (Invalid)
```python
class MinStack:

    def __init__(self):
        self.stack = []
        self.min_heap = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        heapq.heappush(self.min_heap, val)

    def pop(self) -> None:
        _ = self.stack.pop()
        self.min_heap = self.stack.copy()
        heapq.heapify(self.min_heap)

    def top(self) -> int:
        return self.stack[-1] 

    def getMin(self) -> int:
        return self.min_heap[0] 
```
* Time complexity: Suppose the maximum number of elements in the stack is $n$.
	* `push` takes $O(log\ n)$ to heapify.
	* `pop` take $O(n)$ to clone and $O(n)$ to heapify from scratch.
* Space complexity: $O(n)$
	* A min heap maintaining the value ordering takes $O(n)$.
#### 2. Auxiliary Stack as Min Tracker
As stated above, using a single value only will lose track of the minimum value in a stack if this value has been popped out. Instead, we construct another stack to track the minimum value for each value in the original stack.
![[155_min_stack.drawio.png]]
```python
class MinStack:

    def __init__(self):
        self.stack = []
        self.min_tracker = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        if len(self.min_tracker) == 0 or val < self.getMin():
            self.min_tracker.append(val)
        else:
            # Pushed value is greater than current min
            self.min_tracker.append(self.getMin())

    def pop(self) -> None:
        _ = self.stack.pop()
        _ = self.min_tracker.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_tracker[-1]
```
* Time complexity: $O(1)$
* Space complexity: $O(n)$
	* A stack itself takes $O(n)$.
	* A minimum tracker takes $O(n)$.
##### Comment
* We can also define a custom node tracking the pushed value and the current minimum at the same time. Or, we can just store a `(val, min_val)` tuple in the stack.
```python
class Node:
    
    def __init__(self, val: int, min_val: int) -> None:
        self.val = val
        self.min_val = min_val


class MinStack:

    def __init__(self):
        self.stack = []

    def push(self, val: int) -> None:
        if len(self.stack) == 0 or val < self.getMin(): 
            self.stack.append(Node(val, val))
        else:
            self.stack.append(Node(val, self.getMin()))

    def pop(self) -> None:
        _ = self.stack.pop()

    def top(self) -> int:
        return self.stack[-1].val

    def getMin(self) -> int:
        return self.stack[-1].min_val
```
* We can optimize space complexity by tracking necessary minimum values only.
	![[155_2.drawio.png]]
```python
class MinStack:

    def __init__(self):
        self.stack = []
        self.min_tracker = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        if len(self.min_tracker) == 0 or val <= self.getMin():
            self.min_tracker.append(val)

    def pop(self) -> None:
        popped = self.stack.pop()
        if popped == self.getMin():
            _ = self.min_tracker.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_tracker[-1]
```
#### 3. Linked List w/o Python List

### Discussion
* Can we solve this question with $O(1)$ auxiliary complexity?
