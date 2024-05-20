# [167. Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
> Find two indices of numbers adding up to a specific `target` number. There's exactly one solution and no number can be used twice.
### Input
* `numbers`: An integer array.
	* `2 <= numbers.length <= 3 * 10^4`
	* `-1000 <= numbers[i] <= 1000`
	* `numbers` is sorted in **non-decreasing order**.
* `target`: A specific target integer.
	- `-1000 <= target <= 1000`
### Output
An integer array containing two indices of numbers adding up to `target`. Note that the returned indices are **1-indexed**.
### Example
```
Input: numbers = [2, 7, 11, 15], target = 9
Output: [1, 2]
Explanation: The sum of 2 and 7 is 9. Therefore, index1 = 1, index2 = 2. We return [1, 2].
```
### Idea
For the brute-force approach and hash map solution, please refer to [[Hack-the-Algo-and-DS/Grind-LeetCode/src/arrays_and_hashing/1_two_sum/README|1_two_sum]]. Note that this problem requires that solutions should use only **constant** auxiliary space.
#### 1. Two Pointers
The key information is that the input array is already sorted in **non-decreasing order**. For a sorted array, it's intuitive to use two pointers to continuously check the current sum. There are three conditions to consider,
1. If the current sum is equal to `target`, return the current  indices.
2. If the current sum is greater than `target`, move the **right** pointer **leftward** to **decrease** the sum.
3. If the current sum is less than `target`, move the **left** pointer **rightward** to **increase** the sum.
```python
def twoSum(numbers: List[int], target: int) -> List[int]:
    l, r = 0, len(numbers) - 1
    while l < r:
        cur_sum = numbers[l] + numbers[r]
        if cur_sum == target:
            return [l+1, r+1]

        if cur_sum < target:
            l += 1 
        elif cur_sum > target:
            r -= 1


def twoSum(numbers: List[int], target: int) -> List[int]:
    l, r = 0, len(numbers) - 1
    while numbers[l] + numbers[r] != target:
        if numbers[l] + numbers[r] < target:
            l += 1 
        else:
            r -= 1

    return [l+1, r+1]
```
* Time complexity: $O(n)$
	* Each number is visited at most once.
* Space complexity: $O(1)$
### Discussion
* Can we solve the problem with the concept of **binary search**?
* Because there exist constraints on the range of elements in `numbers` and `target`, can we state that the space complexity is **constant** when using a hash map?