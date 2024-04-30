# [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)
> Check if an integer array contains any duplicate.
### Input
* `nums`: An integer array.
### Output
`True` if there's any duplicate else `False`.
### Example
```
Input: nums = [1, 2, 3, 1]
Output: true
```
### Idea
#### 1. Hash Map
Map each element to its occurrence. If the element has existed, return `True` directly without having to traverse `num2freq` again in the end.
```python
def containsDuplicate(nums: List[int]) -> bool:
    num2freq = {}
    for num in nums:
        if num not in num2freq:
            num2freq[num] = 1
        else:
            return True

    return False
```
* Time complexity: $O(n)$
	* Traverse `nums` once.
* Space complexity: $O(n)$
	* Use a hash map to record the occurrences.
#### 2. Neighbor Checking after Sorting
Check if neighboring elements are the same after sorting `nums`.
```python
def containsDuplicate(nums: List[int]) -> bool:
    nums.sort()
    for i in range(len(nums)-1):
        if nums[i] == nums[i+1]:
            return True

    return False
```
* Time complexity: $O(n\ log\ n)$
	* Sorting takes $O(n\ log\ n)$.
	* Neighbor checking takes additional $O(n)$.
* Space complexity: $O(1)$