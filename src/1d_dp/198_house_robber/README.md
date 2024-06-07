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
Input: nums = [1, 2, 3, 1]
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
	    \max (nums[n] + f(n-2), f(n-1))  & \text{otherwise}
	\end{dcases}
```

[![198.png](https://i.postimg.cc/3RD6rzdV/198.png)](https://postimg.cc/YGM8bnNz)
#### 1. Recursive (TLE)
Starting from the most naive solution, we use a simple recursive function, which involves many duplicated computations.
```python
def rob(nums: List[int]) -> int:
    def _max_money_to_house(i: int) -> int:
        if i == 0:
            return nums[0]
        elif i == 1:
            return max(nums[0], nums[1])

        return max(nums[i] + _max_money_to_house(i-2), _max_money_to_house(i-1))

    n_houses = len(nums)
    return _max_money_to_house(n_houses-1)
```
* Time complexity: $O(2^n)$
	* The recurrence can be represented by a binary tree, which takes $O(2^n)$ to complete all computations.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
#### 2. Recursive with Memoization
Again, we incorporate memoization into the solution to avoid duplicated computations.
```python
def rob(nums: List[int]) -> int:
    def _max_money_to_house(i: int) -> int:
        if i == 0:
            return nums[0]
        elif i == 1:
            return max(nums[0], nums[1])
        
        if i in max_money:
            return max_money[i]
        
        max_money[i] = max(nums[i] + _max_money_to_house(i-2), _max_money_to_house(i-1))
        return max_money[i]

    max_money = {}
    n_houses = len(nums)

    return _max_money_to_house(n_houses-1)
```
* Time complexity: $O(n)$
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
	* A hash map caching results that have been derived takes $O(n)$.
#### 3. 1d DP with DP Array
Then, let's move on to the **bottom-up** approach, which iteratively derives and fills the maximum amount of money the robber can rob till each house.
```python
def rob(nums: List[int]) -> int:
    n_houses = len(nums)
    if n_houses == 1:
        return nums[0]
    elif n_houses == 2:
        return max(nums[0], nums[1])
    
    dp = [0] * n_houses
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1]) 
    for i in range(2, n_houses):
        dp[i] = max(nums[i] + dp[i-2], dp[i-1])

    return dp[n_houses-1]
```
* Time complexity: $O(n)$
	* Deriving the maximum amount of money for each house takes $O(1)$, hence $O(n)$ in total.
* Space complexity: $O(n)$
	* A dp array caching results that have been derived takes $O(n)$.
#### 4. 1d DP - Space Optimization
We can see that the current amount only depends on the previous two. Hence, maintaining these two temporary states is sufficient.
```python
def rob(nums: List[int]) -> int:
    n_houses = len(nums)
    if n_houses == 1:
        return nums[0]
    elif n_houses == 2:
        return max(nums[0], nums[1])
    
    prev, cur = nums[0], max(nums[0], nums[1])
    for i in range(2, n_houses):
        prev, cur = cur, max(nums[i] + prev, cur)

    return cur  
```
* Time complexity: $O(n)$
* Space complexity: $O(1)$