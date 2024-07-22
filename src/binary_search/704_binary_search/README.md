# [704. Binary Search](https://leetcode.com/problems/binary-search/)
> Implement binary search on an integer array sorted in ascending order.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 10^4`
	* `-10^4 < nums[i] < 10^4`
	* All the integers in `nums` are **unique**.
	* `nums` is sorted in ascending order.
* `target`: A target integer to find.
	* `-10^4 < target < 10^4`
### Output
The index of `target` if found else `-1`.
### Example
```
Input: nums = [-1, 0, 3, 5, 9, 12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4
```
### Idea
The core idea of binary search is to keep halving the input array until the base case is triggered. There exist two base cases,
1. `target` is found, then the corresponding index is returned.
2. `target` doesn't exist in `nums`, then `-1` is returned.
#### 1. Iterative
For iterative solution, we use two pointers (*i.e.,* left/right or low/high) as the boundaries of the current array chunk to search.
```python
def search(nums: List[int], target: int) -> int:
    l, r = 0, len(nums) - 1
    while l <= r:
        mid = (l + r) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] > target:
            r = mid - 1
        else:
            l = mid + 1

    return -1
```
* Time complexity: $O(log\ n)$
	* As binary search keeps halving the input array, it takes $O(log\ n)$ to find `target`.
* Space complexity: $O(1)$
#### 2. Recursive
Similar to iterative solution, the recursion also keeps halving the input array. Note that we need to define the base case (while is handled by the `while` condition in the iterative solution) to avoid infinite loop. When the left pointer is greater than the right one, it means that `target` doesn't exist in `nums`.
```python
def search(nums: List[int], target: int) -> int:
    def _dfs(l: int, r: int) -> int:
        if l > r:
            return -1

        mid = (l + r) // 2
        if nums[mid] == target:
            return mid
        elif nums[mid] > target:
            return _dfs(l, mid - 1)
        else:
            return _dfs(mid + 1, r)

    return _dfs(0, len(nums) - 1)
```
* Time complexity: $O(log\ n)$
	* As binary search keeps halving the input array, it takes $O(log\ n)$ to find `target`.
* Space complexity: $O(log\ n)$
	* The depth of call stack takes $O(log\ n)$.
### References
* [Binary Search 101](https://leetcode.com/problems/binary-search/solutions/423162/binary-search-101/)
	* "So when your binary search is stuck, think of the situation when there are only 2 elements left."