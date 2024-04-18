# [66. Plus One](https://leetcode.com/problems/plus-one/)
> Add one to the given large integer `digits` and return it in a list as the input format.
### Input
* `digits`: An integer array representing a large integer, ordered from most significant to least significant in left-to-right order.
	* No leading `0`'s.
### Output
An integer array representing the input integer plussed by one.
### Idea
#### 1. Auxiliary Carry Status Tracker 
Use an auxiliary list tracking the carry status and current remainder (*i.e.,* `cur_sum % 10`) on the less significant side.
```python
def plusOne(digits: List[int]) -> List[int]:
    n_plus_1 = [0 for _ in range(len(digits))] + [1]
    for i in range(len(n_plus_1) - 1, 0, -1):
        cur_sum = digits[i - 1] + n_plus_1[i]

        # Derive the remainder and carry
        n_plus_1[i] = cur_sum % 10
        n_plus_1[i - 1] = cur_sum // 10
    n_plus_1 = n_plus_1 if n_plus_1[0] == 1 else n_plus_1[1:]

    return n_plus_1
```
* Time complexity: $O(n)$
	* Traverse `digits` only once.
*  Space complexity: $O(n)$
	* Use an additional auxiliary list `[0, 0, 0, ..., 1]`.
#### 2. Carry Propagation or Not
There are two conditions for each iteration,
1. No carry: Add one and return `digits` if the digit is less than 9.
2. Have carry: Set the current digit to `0` and continue traversing (*i.e.,* propagate carry).
Finally, insert most significant `1` if all numbers in `digits` are `9`, meaning propagate carry through the whole input list.
```python
def plusOne(digits: List[int]) -> List[int]:
    for i in range(len(digits) - 1, -1, -1):
        if digits[i] < 9:
            digits[i] += 1
            return digits
            
	    # Propagate carry
        digits[i] = 0

    return [1] + digits
```
* Time complexity: $O(n)$
	* Traverse `digits` only once.
*  Space complexity: $O(1)$
#### 3. Recursion