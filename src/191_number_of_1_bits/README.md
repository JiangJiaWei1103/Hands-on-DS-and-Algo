# [191. Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)
> Return the number of **set bits** of a positive integer.
* A set bit refers to a bit in the binary representation of a number that has a value of `1`.
### Input
* `n`: A positive integer.
### Output
The number of set bits.
### Example
```
Input: n = 11
Output: 3
Explanation: The input binary string 1011 has a total of three set bits.
```
### Idea
#### 1. Quotient and Remainder
Yield the binary representation of `n` by continuously dividing `n` by 2. Increase the number of set bits if the current remainder is `1`.
```python
def hammingWeight(n: int) -> int:
    n_set_bits = 0
    q = n  # Quatient
    while q != 0:
        r = q % 2  # Remainder
        if r == 1:
            n_set_bits += 1
        
        q = q // 2  

    return n_set_bits
```
* Time complexity: $O(1)$
	* `n` is a 32-bit integer, which guarantees that the number of iterations is no more than 32 times.
* Space complexity: $O(1)$
#### 2. Bitwise And with Right Shift
Check the leftmost bit using `& 1` and right shift `n`.
```python
def hammingWeight(n: int) -> int:
    n_set_bits = 0
    while n != 0:
        n_set_bits += (n & 1)
        n >>= 1

    return n_set_bits
```
* Time complexity: $O(1)$
	* `n` is a 32-bit integer, which guarantees that the number of iterations is no more than 32 times.
* Space complexity: $O(1)$
#### 3. Brian Kernighan's Algorithm
Continuously remove the leftmost `1` in `n` by applying `n & (n-1)`.
[![191-number-of-1-bits-drawio.png](https://i.postimg.cc/gcqycHNb/191-number-of-1-bits-drawio.png)](https://postimg.cc/4nyHBtDW)
```python
def hammingWeight(n: int) -> int:
    n_set_bits = 0
    while n != 0:
        n &= (n-1)
        n_set_bits += 1
        
    return n_set_bits
```
* Time complexity: $O(1)$
	* Number of iterations is equal to the number of `1`'s in `n`.
* Space complexity: $O(1)$
#### 4. [The Second Solution Here](https://leetcode.com/problems/number-of-1-bits/solutions/1044775/python-n-n-1-trick-even-faster-explained/)
