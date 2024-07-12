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
* Space complexity: $O(1)$
#### 2. Sorting + Two Pointers
This problem can be viewed as an extension of [[Hack-the-Algo-and-DS/Grind-LeetCode/src/two_pointers/167_two_sum_ii_input_array_is_sorted/README|167. Two Sum II]]. Concretely speaking, we **dynamically set a new target as a pivot** and use two pointers to find all possible triplets. The key difference is that the answer isn't unique.<br>
The process is summarized as follows,
1. Sort the array in **non-decreasing order**.
2. For each pivot, find all triplets adding up to zero.

There's techniques to somewhat speedup the algorithm. If `nums[pivot] > 0`, we can directly stop the algorithm because all elements on the right side of `nums[pivot]` must be greater than or equal to `nums[pivot]`.
```python
def threeSum(nums: List[int]) -> List[List[int]]:
    nums.sort()
    res = []
    for pivot, num in enumerate(nums):
        if pivot > 0 and num == nums[pivot - 1]:
            continue

        l, r = pivot + 1, len(nums) - 1
        while l < r:
            cur_sum = num + nums[l] + nums[r]
            if cur_sum < 0:
                l += 1
            elif cur_sum > 0:
                r -= 1
            else:
                res.append([num, nums[l], nums[r]])
                l += 1
                while l < r and nums[l] == nums[l-1]:
                    l += 1

    return res
```
* Time complexity: $O(n^2)$
	* Sorting takes $O(n\ log\ n)$.
	* For each target, we need to search all possible triplets, which takes $O(n^2)$.
* Space complexity: $O(1)$
	* There's no explicit auxiliary space. However, the aux space might be used implicitly by the sorting algorithm.
##### Comment
* Note that we only update one pointer when we find a valid triplet because the `if-else` condition can handle the other one automatically.
### Discussion
* Can we solve this problem without sorting? Does a hash map help? How to avoid a linear scan on output triplets to avoid duplicates?
	* Running two sum with a hash map $n$ times takes TC $O(n^2)$ and SC $O(n)$.