# [268. Missing Number](https://leetcode.com/problems/missing-number/)
> Find the only missing number in an array containing **distinct** numbers within the range `[0, n]`.
### Input
* `nums`: An integer array of range `[0, n]` with one number missing.
### Output
The missing number.
### Example
```
Input: nums = [3, 0, 1]
Output: 2
```
### Idea
#### 1. Summation
Subtract sum of `nums` from sum of the complete range.
```python
def missingNumber(nums: List[int]) -> int:
    n = len(nums)
    missing = n * (n+1) / 2 - sum(nums)

    return missing
```
* Time complexity: $O(n)$
	* Derive sum of `nums`.
* Space complexity: $O(1)$
##### Comment
* Note the integer overflow issue when `n`'s upper bound is greater.
#### 2. Traversing after Sorting
Sort `nums`, then return the index where the index and the visited number mismatches.
* Time complexity: $O(n\ log\ n)$
* Space complexity: $O(1)$
#### 3. XOR
Add a dummy index, `len(nums)`, and use XOR operations to offset the corresponding index and  number pairs.
[![268-missing-number-drawio.png](https://i.postimg.cc/43MKDSg6/268-missing-number-drawio.png)](https://postimg.cc/VSqLM45v)
```python
def missingNumber(nums: List[int]) -> int:
    n = len(nums)
    missing = 0 ^ n
    for i in range(n):
        missing ^= i ^ nums[i]
    
    return missing
```
* Time complexity: $O(n)$
	* Loop through `nums` once.
	* [ ] Analyze the time complexity of bitwise XOR operations.
* Space complexity: $O(1)$
#### 4. Binary Search