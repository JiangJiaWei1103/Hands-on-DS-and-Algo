# [213. House Robber II](https://leetcode.com/problems/house-robber-ii/)
> Return the maximum amount of money you can rob without alerting the police. All houses are **arranged in a circle**. Note that the police will be contacted if two adjacent houses are broken into on the same night.
### Input
* `nums`: An integer array containing the amount of money.
	* `1 <= nums.length <= 100
	* `0 <= nums[i] <= 1000`
### Output
The maximum amount of money you can rob.
### Example
```
Input: nums = [1, 2, 3, 1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```
### Idea
Now, you're a well-trained robber who knows how to rob houses **arranged in a sequence**. For houses arranged in a circle, you notice that there exists another limitation. You can't rob the first and last house at the same time. Hence, it becomes clear that the problem can be broken down into two sub-problems,
1. Rob houses `0` to `n-2`.
2. Rob houses `1` to `n-1` .
Now, you can reuse what you've learned to rob safely!
#### 1. Rob Twice
The circular houses are broken down into two sequences and robbed separately. Finally, the robber will pick the one with the maximum amount of money.<br>
As illustrated below, we set `rob = 0` to handle the initial case $f(1)$ (see the first three houses including the dummy one),
1. Rob the green house plus the maximum money robbed till the dummy.
2. Don't rob the green house, take the maximum money robbed till the blue.

[![213.png](https://i.postimg.cc/KcLQF1Q3/213.png)](https://postimg.cc/V5fXWLxz)

```python
def rob(nums: List[int]) -> int:
    def _rob(nums: List[int]) -> int:
        n_houses = len(nums)
        rob, skip = 0, nums[0]
        for i in range(1, n_houses):
            rob, skip = skip, max(skip, nums[i] + rob)

        return skip 

    if len(nums) == 1:
        return nums[0]
    max_money = max(_rob(nums[1:]), _rob(nums[:-1]))

    return max_money 
```
* Time complexity: $O(n)$
	* Deriving the maximum amount of money for each house takes $O(1)$, hence $O(2n) \approx O(n)$ in total.
	* Copying split `nums` arrays takes $O(n)$.
* Space complexity: $O(n)$
	* Copying split `nums` arrays takes $O(n)$.
##### Comment
* This problem can be solved without auxiliary space complexity by avoiding copying `nums` arrays.
```python
def rob(nums: List[int]) -> int:
    def _rob(start: int, end: int) -> int:
        rob, skip = 0, nums[start]
        for i in range(start+1, end):
            rob, skip = skip, max(skip, nums[i] + rob)

        return skip 

    n_houses = len(nums)
    if n_houses == 1:
        return nums[0]
    max_money = max(_rob(0, n_houses-1), _rob(1, n_houses))

    return max_money
```
