# [136. Single Number](https://leetcode.com/problems/single-number/)
> Find the element appearing only once in a **non-empty** array. Note that all the others appear exactly twice. Solve the problem with **linear TC** and **constant SC**.
### Input
* `nums`: An integer array.
### Output
An element appearing only once.
### Example
```
Input: nums = [4, 1, 2, 1, 2]
Output: 4
```
### Idea
#### 1. Hash Map
Map each number to its occurrences, then return the one appearing only once.
* Time complexity: $O(n)$
	* Traverse an array once.
	* Traverse the hash map at most once.
* Space complexity: $O(n)$
#### 2. Sorting and Group Check
Sort `nums` and check two elements at a time (*i.e.,* chunk-wise checking). Return the first element in the chunk if two elements are different.
* Time complexity: $O(n\ log\ n)$
	* Sorting takes $O(n\ log\ n)$.
	* Chunk-wise checking takes $O(n)$.
* Space complexity: $O(1)$
#### 3. Bitwise XOR
Use the property of XOR to offset the cumulative sum of numbers appearing twice. Then, return the remaining number, representing the only single number.
```python
def singleNumber(nums: List[int]) -> int:
    base = 0
    for num in nums:
        base ^= num

    return base
```
* Time complexity: $O(n)$
	* Traverse an array once.
* Space complexity: $O(1)$
##### Comment
* Properties of XOR are summarized as follows,
	* `a ^ 0 = a` and `a ^ a = 0`
	* Commutative: `a ^ b = b ^ a`
	* Associative: `(a ^ b) ^ c = a ^ (b ^ c)`