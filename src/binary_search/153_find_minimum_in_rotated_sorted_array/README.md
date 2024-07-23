# [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
> Find the minimum element in a rotated sorted array.
* **Rotating** an array `[a[0], a[1], a[2], ..., a[n-1]]` 1 time results in the array `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]`.
### Input
* `nums`: An integer array.
	* `n == nums.length`
	* `1 <= n <= 5000`
	* `-5000 <= nums[i] <= 5000`
	* All the integers of `nums` are **unique**.
	* `nums` is sorted and rotated between `1` and `n` times.
### Output
The minimum element of the integer array.
### Example
```
Input: nums = [3,4,5,1,2]
Output: 1
Explanation: The original array was [1,2,3,4,5] rotated 3 times.
```
### Idea
There are many solutions to solve this problem, but we need to make sure the time complexity is $O(log\ n)$. Hence, sorting then running binary search, linear search, min heap, etc. are all unqualified.
#### 1. Binary Search - Inflection Point
To solve this problem in $O(log\ n)$, we can use binary search to find the minimum. The key point is to find the inflection point, at which the value decreases. For an array under search, we have the following information to use,
1. `nums[l] < nums[r]`: The array is already sorted, and we can guarantee the leftmost element is the minimum of this array.
	* That is, the number of rotations is equal to the number of elements in this array, which can be interpreted as no rotation.
2. `nums[l] > nums[r]`: The array is rotated, and we need to find which half the inflection point is located in.
	* `nums[mid] < nums[l]`: The inflection point is on the left half, or **`mid` is already the minimum**.
	* `nums[mid] >= nums[l]`: The left half is sorted, so the inflection is on the right half.
```python
def findMin(nums: List[int]) -> int:
    res = nums[0]
    l, r = 0, len(nums) - 1
    while l <= r:
        if nums[l] < nums[r]:
            # nums[l] must be the min of nums[l..r]
            # no need to search anymore
            return min(res, nums[l])

        # Position the inflection point
        mid = l + (r - l) // 2
        res = min(res, nums[mid])
        if nums[mid] < nums[l]:
            # Inflection is on the left half
            # or mid is already the minimum
            r = mid - 1
        else:
            # Left half is sorted
            # inflection is on the right half
            l = mid + 1

    return res
```
* Time complexity: $O(log\ n)$
	* Keeping halving the array during search helps reduce the runtime to $O(log\ n)$.
* Space complexity: $O(1)$