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
The core idea of binary search is that we can keep splitting the input array **in half**.
#### 1. Iterative
For iterative solution, we use two pointers (*i.e.,* left and right) as the boundaries to keep shrinking the array chunk at which we're looking.
```python
def search(nums: List[int], target: int) -> int:
    l, r = 0, len(nums) - 1
    while l <= r:
        m = (l + r) // 2
        if nums[m] == target:
            return m
        elif nums[m] < target:
            l = m + 1
        else:
            r = m - 1

    return -1
```
* Time complexity: $O(log\ n)$
	* As binary search keeps halving the input array, it takes $O(log\ n)$ to find `target`.
* Space complexity: $O(1)$
#### 2. Recursive
Similar to iterative solution, the recursive also keeps halving the input array. Note that we need to define the base case to avoid infinite loop. When the left pointer is greater than the right one, it means that `target` can't be found in `nums`.
```python
def search(nums: List[int], target: int) -> int:
    def _search(l: int, r: int) -> int:
        # Base case
        if l > r:
            return -1

        m = (l + r) // 2
        if nums[m] == target:
            return m
        elif nums[m] < target:
            return _search(m + 1, r)
        else:
            return _search(l, m - 1)
        
    return _search(0, len(nums) - 1)
```
* Time complexity: $O(log\ n)$
	* As binary search keeps halving the input array, it takes $O(log\ n)$ to find `target`.
* Space complexity: $O(log\ n)$
	* The depth of call stack takes $O(log\ n)$.
### References
* [Binary Search 101](https://leetcode.com/problems/binary-search/solutions/423162/binary-search-101/)
	* "So when your binary search is stuck, think of the situation when there are only 2 elements left."