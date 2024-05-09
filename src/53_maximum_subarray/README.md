# [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)
> Find the subarray with the **largest** sum in an integer array.
### Input
* `nums`: An integer array.
### Output
The largest sum of the subarray.
### Example
```
Input: nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6
Explanation: The subarray [4, -1, 2, 1] has the largest sum 6.
```
### Idea
#### 1. Brute-Force
Use a nested loop to find a subarray with the largest sum starting from each position `i`.
```python
# Skipped
```
* Time complexity: $O(n^2)$
* Space complexity: $O(1)$
#### 2. DP Array
The problem can be divided into sub-problems with the relationship shown as follows,
```
# Let f = maxSubArray
f(nums, i) = nums[i] + f(nums, i-1) if f(nums, i-1) > 0 else nums[i]
```
If the largest sum till the previous position is a **negative** number, adding it with the current number will always make the sum smaller.
```python
def maxSubArray(nums: List[int]) -> int:
    cur_max = [nums[0]]  # Record maximum sum of [0:i+1] for each pos i
    max_so_far = nums[0]
    for i in range(1, len(nums)):
        cur_max_till_i = cur_max[i-1] + nums[i] if cur_max[i-1] >= 0 else nums[i] 
        cur_max.append(cur_max_till_i)

        # Update the global maximum sum
        max_so_far = max(max_so_far, cur_max_till_i)

    return max_so_far 
```
* Time complexity: $O(n)$
	* Traverse `nums` once.
* Space complexity: $O(n)$
	* Record the largest sum till position `i` for each `i`.
#### 3. Better DP - Kadane's Algorithm
```python
def maxSubArray(nums: List[int]) -> int:
    cur_max, max_so_far = 0, float("-inf")
    for num in nums:
        cur_max += num
        if cur_max > max_so_far:
            max_so_far = cur_max

        if cur_max < 0:
            cur_max = 0

    return cur_max


def maxSubArray(nums: List[int]) -> int:
    """More concise."""
    cur_max, max_so_far = nums[0], nums[0]
    for i in range(1, len(nums)):
        cur_max = max(0, cur_max+nums[i])
        max_so_far = max(max_so_far, cur_max)

    return max_so_far
```
* Time complexity: $O(n)$
	* Traverse `nums` once.
* Space complexity: $O(1)$
### References
* [[C++/Python] 7 Simple Solutions w/ Explanation | Brute-Force + DP + Kadane + Divide & Conquer](https://leetcode.com/problems/maximum-subarray/solutions/1595195/c-python-7-simple-solutions-w-explanation-brute-force-dp-kadane-divide-conquer/)
	* Keep optimizing from brute-force to Kadane's.