# [1. Two Sum](https://leetcode.com/problems/two-sum/)
> Find indices of two numbers in `nums` adding up to `target`. There's exactly one solution and no number can be used twice.
### Input
* `nums`: An integer array.
	* `2 <= nums.length <= 10^4`, containing at least two integers.
	- `-10^9 <= nums[i] <= 10^9`
* `target`: A target integer.
	- `-10^9 <= target <= 10^9`
### Output
Indices of two numbers adding up to `target`.
### Example
```
Input: nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
```
### Idea
#### 1. Brute-force
The most intuitive way is to sum up all possible number pairs and check if the result is equal to `target`. We can implement this idea with a nested loop.
```python
def twoSum(nums: List[int], target: int) -> List[int]:
    n_nums = len(nums)
    for i in range(n_nums):
        for j in range(i+1, n_nums):
            if nums[i] + nums[j] == target:
                return [i, j]
```
* Time complexity: $O(n^2)$
	* There are at most $\frac{n(n-1)}{2}$ pairs to check.
*  Space complexity: $O(1)$
#### 2. Hash Map
To avoid checking all possible pairs, we can use the concept of **complement**. That is, if `nums[i] + nums[j] == target`, the number at the position `i` is the complement of that at `j`. The implementation can be done with a hash map, which maps the complement of the current number to the current index.
```python
def twoSum(nums: List[int], target: int) -> List[int]:
    complement2idx = {}
    for i, num in enumerate(nums):
        if num in complement2idx:
            # The pair is found
            return [complement2idx[num], i]

        complement2idx[target-num] = i
```
* Time complexity: $O(n)$
	* `nums` is traversed at most once.
* Space complexity: $O(n)$
	* A hash map recording the complement of each element in `nums` takes $O(n)$.
#### 3. Two Pointers
For a sorted array, it's easy to find the number pair adding up to `target` by checking the sum from two ends with two pointers. There are 3 conditions summarized as follows,
1. If the current sum is equal to `target`, then the original indices of these two numbers are returned.
2. If the current sum is greater than `target`, the **right** pointer should be moved **left** to decrease the value.
3. If the current sum is less than `target`, the **left** pointer should be moved **right** to increase the value.
Note that the original `nums` is **unsorted**, so we need to record the **original index** of each element to avoid information loss after sorting.
```python
def twoSum(nums: List[int], target: int) -> List[int]:
    num2idx = [(num, i) for i, num in enumerate(nums)]
    num2idx.sort()

    l, r = 0, len(num2idx) - 1
    while l < r:
        cur_sum = num2idx[l][0] + num2idx[r][0]
        if cur_sum == target:
            return [num2idx[l][1], num2idx[r][1]] 
        elif cur_sum > target:
            r -= 1
        else:
            l += 1
```
* Time complexity: $O(n\ log\ n)$
	* Sorting takes $O(n\ log\ n)$.
	* To record original indices, we need to first scan `nums`, which takes $O(n)$.
	* The final sum checking takes another $O(n)$.
* Space complexity: $O(n)$
	* A list of tuples used to record original indices takes $O(n)$.