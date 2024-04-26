# [326. Power of Three](https://leetcode.com/problems/power-of-three/)
> Determine whether an integer is a power of three or not.
### Input
* `n`: An integer.
### Output
`True` if an integer is a power of three else `False`
### Example
```
Input: n = 27
Output: true
```
### Idea
#### 1. Iterative
Keep dividing `n` by 3. Return `True` if the last quotient is equal to `1`.
```python
def isPowerOfThree(n: int) -> bool:
    if n <= 0:
        return False
    
    while n % 3 == 0:
        n = n // 3

    return n == 1
```
* Time complexity: $O(log_3\ n)$
	* The number of iterations is at most the power.
* Space complexity: $O(1)$
#### 2. Recursive
Keep checking if `n // 3` is the power of three within the call stack.
```python
def isPowerOfThree(n: int) -> bool:
    if n == 1:
        return True
    elif n % 3 != 0 or n <= 0:
        return False

    return isPowerOfThree(n // 3)


def isPowerOfThree(n: int) -> bool:
    if n <= 1:
        return n == 1

    return n % 3 == 0 and isPowerOfThree(n // 3)
```
* Time complexity: $O(log_3\ n)$
	* The number of function calls is at most the power.
* Space complexity: $O(log_3\ n)$
	* The depth of the call stack can grow to $log_3\ n$.
#### 3. Divisibility Check w/ the Largest Power of 3
Leverage the property of a prime number.
```python
def isPowerOfThree(n: int) -> bool:
    """The largest power of 3 is less than 2^31."""
    return n > 0 and 1162261467 % n == 0
```
* Time complexity: $O(1)$
* Space complexity: $O(1)$