# [198. House Robber](https://leetcode.com/problems/house-robber/)
> Return the maximum amount of money you can rob without alerting the police. Note that the police will be contacted if two adjacent houses are broken into on the same night.
### Input
* `input`: An integer array containing the amount of money.
	* `1 <= nums.length <= 100`
	- `0 <= nums[i] <= 400`
### Output
The maximum amount of money you can rob.
### Example
```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```
### Idea
Because two adjacent houses can't be robbed in a row, the robber has two choices when standing in front of the $i$-th house,
1. Rob it, then the robber must skip the $(i-1)$-th house.
2. Don't rob it, then the robber can rob till the $(i-1)$-th house.

Let $f(n)$ be the maximum money robbed till the $n$-th house, we define the recurrence relationship as follows,
```math
	f(n)= 
	\begin{dcases}
	    nums[0]                  & \text{if } n = 0 \\
	    \max (nums[0], nums[1])  & \text{if } n = 1 \\
	    \max (f(n-2) + nums[n], f(n-1))  & \text{otherwise}
	\end{dcases}
```

[![198.png](https://i.postimg.cc/3RD6rzdV/198.png)](https://postimg.cc/YGM8bnNz)
#### 1. Recursive (TLE)
Starting from the most naive solution, we use a simple recursive function, which involves many **duplicated computations**.
```python
def rob(nums: List[int]) -> int:
    def _dfs(i: int) -> int:
        if i == 0:
            return nums[0]
        elif i == 1:
            return max(nums[0], nums[1])
        
        return max(_dfs(i-2) + nums[i], _dfs(i-1))

    return _dfs(len(nums) - 1)
```
* Time complexity: $O(2^n)$
	* The recurrence can be represented by a binary tree, which takes $O(2^n)$ to complete all computations.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
#### 2. Recursive with Memoization
Again, we incorporate **memoization** into the solution to avoid duplicated computations.
```python
def rob(nums: List[int]) -> int:
    def _dfs(i: int) -> int:
        if i in memo:
            return memo[i]
        elif i == 1:
            memo[i] = max(nums[0], nums[1])
        else: 
            memo[i] = max(_dfs(i-2) + nums[i], _dfs(i-1))

        return memo[i]

    memo = {0: nums[0]}

    return _dfs(len(nums) - 1)
```
* Time complexity: $O(n)$
	* Eliminating duplicated computations makes the binary tree become nearly linear, which takes $O(n)$ to traverse.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
	* A hash map caching results that have been derived takes $O(n)$.
#### 3. 1d DP with DP Array
Then, let's move on to the **bottom-up** approach, which iteratively derives and fills the maximum amount of money the robber can rob till each house.
```python
def rob(nums: List[int]) -> int:
    if len(nums) == 1:
        return nums[0]

    dp = [0] * len(nums)
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1])
    for i in range(2, len(nums)):
        dp[i] = max(dp[i-2] + nums[i], dp[i-1])

    return dp[-1]
```
* Time complexity: $O(n)$
	* Deriving the maximum amount of money for each house takes $O(1)$, hence $O(n)$ in total.
* Space complexity: $O(n)$
	* A dp array caching results that have been derived takes $O(n)$.
#### 4. 1d DP - Space Optimization
We can see that the current amount only depends on the previous two. Hence, maintaining these two temporary states is sufficient. We can just in-place modify `nums` to store the temporary max money if it's valid.
```python
def rob(nums: List[int]) -> int:
    if len(nums) == 1:
        return nums[0]

    prev = nums[0]
    cur = max(nums[0], nums[1])
    for i in range(2, len(nums)):
        prev, cur = cur, max(prev + nums[i], cur)

    return cur


def rob(nums: List[int]) -> int:
    if len(nums) == 1:
        return nums[0]

    nums[1] = max(nums[0], nums[1])
    for i in range(2, len(nums)):
        nums[i] = max(nums[i-2] + nums[i], nums[i-1]) 

    return nums[-1]
```
* Time complexity: $O(n)$
* Space complexity: $O(1)$