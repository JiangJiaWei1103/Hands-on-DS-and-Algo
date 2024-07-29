# [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
> Find a target in a rotated sorted array.
* `nums` is possibly rotated at an unknown pivot index `k` (`1 <= k < nums.length`) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]`.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 5000`
	- `-10^4 <= nums[i] <= 10^4`
	- All values of `nums` are **unique**.
	- `nums` is an ascending array that is possibly rotated.
* `target`: A target integer to search.
	* `-10^4 <= target <= 10^4`
### Output
The index of `target` if found, else `-1`.
### Idea
First, the rotation operation can be thought of as a process of cutting `nums` at `k` and splicing two portions in a reversed order. To solve the problem with $O(log\ n)$ time complexity, we can't simply do a linear search. Regarding $O(log\ n)$ time complexity, the binary search algorithm is a good starting point.
#### 1. Binary Search
Because the input array isn't completely sorted, we can't apply the normal binary search. The key points of the binary search algorithms are summarized as follows,
1. If we find `target` at the middle point, the index of the middle point is returned.
2. If `target` isn't at the middle point, we need to decide which portion to go, either left or right.

In this problem, we know that one half of `nums` is sorted, and the inflection point (i.e., the pivot) is on the other side. With this information, we can determine which portion to go to continue the binary search algorithm. The process can be defined as follows,
1. Determine which portion is sorted.
2. Check if `target` **falls within the sorted portion** and decide which portion to go.
```python
def search(nums: List[int], target: int) -> int:
    l, r = 0, len(nums) - 1
    while l <= r:
        mid = l + (r - l) // 2

        if nums[mid] == target:
            return mid

        if nums[l] <= nums[mid]:
            # Left portion is sorted, the pivot is on the right
            # = is the must, because we use the lower middle
            if nums[l] <= target < nums[mid]:
                r = mid - 1
            else:
                l = mid + 1
        else:
            # Right portion is sorted, the pivot is on the left
            if nums[mid] < target <= nums[r]:
                l = mid + 1
            else:
                r = mid - 1

    return -1
```
* Time complexity: $O(log\ n)$
	* Running the binary search on `nums` takes $O(log\ n)$.
* Space complexity: $O(1)$


