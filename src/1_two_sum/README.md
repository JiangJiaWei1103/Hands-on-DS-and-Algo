# [1. Two Sum](https://leetcode.com/problems/two-sum/)
> Return indices of two numbers in `nums` adding up to `target`. There's exactly one solution and no number can be used twice.
### Input
* `nums`: An array of integers.
* `target`: A target integer.
### Output
Indices of two numbers adding up to `target`.
### Example
```
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
```
### Idea
#### 1. Brute Force
Traverse all pairs of `nums` using a **nested loops** to check the target sum.
```python
def twoSum(nums: List[int], target: int) -> List[int]:
	nums_len = len(nums)
	for i in range(nums_len):
		for j in range(i+1, nums_len):
			if nums[i] + nums[j] == target:
				return [i, j]
			
	# No pair adds up to target, impossible in this problem
	return []
```
* Time complexity: $O(n^2)$
	* Traversing all pairs takes at most $\frac{n(n-1)}{2}$ times to finish.
*  Space complexity: $O(1)$
#### 2. Hash Map
Map difference (complement) of visited number to its own index. Then, return the correct pair if a complement is met at any following iteration.
```python
def twoSum(nums: List[int], target: int) -> List[int]:
    diff2self_idx = {}
    for i, cur_num in enumerate(nums):
        if cur_num in diff2self_idx:
            return [diff2self_idx[cur_num], i]
        
        diff = target - cur_num
        diff2self_idx[diff] = i

    # No pair adds up to target, impossible in this problem
    return []
```
* Time complexity: $O(n)$
	* Traverse `nums` at most once.
	* Hash map lookup takes only **constant time**.
* Space complexity: $O(1)$