# [15. 3Sum](https://leetcode.com/problems/3sum/)
> Find all triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.
### Input
* `nums`: An integer array.
	* `3 <= nums.length <= 3000`
	* `-10^5 <= nums[i] <= 10^5`
### Output
A list containing all triplets summing up to `0`.
### Example
```
Input: nums = [-1, 0, 1, 2, -1, -4]
Output: [[-1, -1, 2],[-1, 0, 1]]
Explanation: 
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0.
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0.
The distinct triplets are [-1, 0, 1] and [-1, -1, 2].
Notice that the order of the output and the order of the triplets does not matter.
```
### Idea
#### 1. Brute-force
The most intuitive solution is to **enumerate** all possible triplets.
```python

```
* Time complexity: $O(n^3)$
	* Enumerating all possible triplets `[i, j, k]` takes $O(n^3)$.
* Space complexity: $O()$
#### 2. Sorting + Two Pointers
This problem can be viewed as an extension of [[Hack-the-Algo-and-DS/Grind-LeetCode/src/arrays_and_hashing/1_two_sum/README|1_two_sum]]. Concretely speaking, we **dynamically set a new target** and use two pointers to find the triplet. The key difference is that the answer isn't unique.<br>
The process is summarized as follows,
1. Sort the array in **non-decreasing order**.
2. For each unique **two-sum target**, represented by `0 - nums[i]`, find all unique element pairs adding up to this target.
	* These two elements are tracked by two pointers.

There exist many techniques to speedup the algorithm, which are summarized as follows,
1. If `nums[i] > 0`, we can directly stop the process because all elements on the right side of `nums[i]` must be greater than or equal to `nums[i]`.
2. When we record a unique triplet, we can skip all duplicated elements without repetitively checking the same triplet.
```python
def threeSum(nums: List[int]) -> List[List[int]]:
    nums.sort()
    i = 0
    triplets = []
    while i <= len(nums) - 2:
        cur_target = 0 - nums[i]
        if cur_target < 0:
            # All elements on the right side of position i must be pos
            break

        l, r = i + 1, len(nums) - 1
        while l < r:
            l_num, r_num = nums[l], nums[r]
            if l_num + r_num == cur_target:
                triplets.append([nums[i], l_num, r_num])

                # Find the next unique elements
                while l < len(nums) and nums[l] == l_num:
                    l += 1
                while r > i and nums[r] == r_num:
                    r -= 1
                continue

            while l < r and nums[l] + r_num < cur_target:
                l += 1
                continue

            while r > l and l_num + nums[r] > cur_target:
                r -= 1

        while i < len(nums) and 0 - nums[i] == cur_target:
            # Skip duplicated targets
            i += 1

    return triplets


def threeSum(nums: List[int]) -> List[List[int]]:
    nums.sort()
    i = 0
    triplets = []
    while i <= len(nums) - 2:
        cur_target = 0 - nums[i]
        if cur_target < 0:
            break

        l, r = i + 1, len(nums) - 1
        while l < r:
            l_num, r_num = nums[l], nums[r]
            if l_num + r_num < cur_target:
                l += 1
            elif l_num + r_num > cur_target:
                r -= 1
            else:
                triplets.append([nums[i], l_num, r_num])
                while l < r and nums[l] == l_num:
                    l += 1
                while r > l and nums[r] == r_num:
                    r -= 1

        while i < len(nums) and 0 - nums[i] == cur_target:
            i += 1

    return triplets
```
* Time complexity: $O(n^2)$
	* Sorting takes $O(n\ log\ n)$.
	* For each target, we need to search all possible triplets, which takes $O(n^2)$.
* Space complexity: $O()$
### Discussion
* Can we solve this problem without sorting?
* What's the space complexity of output triplets?
