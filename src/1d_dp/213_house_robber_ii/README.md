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
Input: nums = [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2), because they are adjacent houses.
```
### Idea
Now, you're a well-trained robber who knows how to rob houses **arranged in a sequence**. For houses arranged in a circle, you notice that there exists another limitation. You can't rob the first and last house at the same time. Hence, it becomes clear that the problem can be broken down into two sub-problems,
1. Rob houses `0` to `n-2`.
2. Rob houses `1` to `n-1` .
Now, you can reuse what you've learned to rob safely!
#### 1. Rob Twice
The circular houses are broken down into two sequences and robbed separately. Finally, the robber will pick the one with the maximum amount of money.

For implementation, we define a reusable function to derive the max money that can be robbed in a sequence of houses. Either recursive or iterative solution is feasible.
```python
def rob(nums: List[int]) -> int:
    def _dfs(nums: List[int], i: int) -> int:
        if i in memo:
            return memo[i]
        elif i == 1:
            memo[1] = max(nums[0], nums[1])
        else:
            memo[i] = max(_dfs(nums, i-2) + nums[i], _dfs(nums, i-1))

        return memo[i]

    if len(nums) == 1:
        return nums[0]

    memo = {0: nums[0]}
    m1 = _dfs(nums[:-1], len(nums)-2)
    memo = {0: nums[1]}
    m2 = _dfs(nums[1:], len(nums)-2)

    return max(m1, m2)


def rob(nums: List[int]) -> int:
    def _rob(nums: List[int]) -> int:
        prev, cur = nums[0], max(nums[0], nums[1])
        for i in range(2, len(nums)):
            prev, cur = cur, max(prev + nums[i], cur)

        return cur
    
    if len(nums) == 1:
        return nums[0]
    
    return max(_rob(nums[1:]), _rob(nums[:-1]))
```
* Time complexity: $O(n)$
	* Deriving the maximum amount of money for each house takes $O(1)$, hence $O(2n) \approx O(n)$ in total.
	* Copying split `nums` arrays takes $O(n)$.
* Space complexity: $O(n)$
	* Copying split `nums` arrays takes $O(n)$.
	* For recursion, additional call stack takes $O(n)$.
##### Comment
* This problem can be solved without auxiliary space complexity by avoiding copying `nums` arrays.
```python
def rob(nums: List[int]) -> int:
    def _rob(s: int, e: int) -> int:
        """Return the max money robbed from the house s to house e."""
        prev, cur = nums[s], max(nums[s], nums[s+1])
        for i in range(s+2, e+1):
            prev, cur = cur, max(prev + nums[i], cur)

        return cur
    
    if len(nums) == 1:
        return nums[0]
    elif len(nums) == 2:
        return max(nums[0], nums[1])

    return max(_rob(0, len(nums)-2), _rob(1, len(nums)-1))
```

%% As illustrated below, we set `rob = 0` to handle the initial case $f(1)$ (see the first three houses including the dummy one),
1. Rob the green house plus the maximum money robbed till the dummy.
2. Don't rob the green house, take the maximum money robbed till the blue.

[![213.png](https://i.postimg.cc/KcLQF1Q3/213.png)](https://postimg.cc/V5fXWLxz) %%