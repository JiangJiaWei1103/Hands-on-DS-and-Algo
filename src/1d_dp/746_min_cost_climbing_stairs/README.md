# [746. Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)
> Calculate the minimum cost to reach the top of a stair. Note that each step of a stair has a cost, and one can climb one or two steps after paying. You can start from either step `0` or `1`.
### Input
* `cost`: An integer array containing the cost of each step of a stair.
	* `2 <= cost.length <= 1000`
	* `0 <= cost[i] <= 999`
### Output
The minimum cost to reach the top of the stair.
### Example
```
Input: cost = [10,15,20]
Output: 15
Explanation: You will start at index 1.
- Pay 15 and climb two steps to reach the top.
The total cost is 15.
```
### Idea
This problem is similar to climbing a stair without costs. Because we need to pay to climb, we take costs into consideration in our recurrence relationship. There exist two ways to formulate the problem,
1. Let $f(n)$ be the min cost to climb to step $n$, the recurrence can be defined as follows,
```math
	f(n)= 
	\begin{dcases}
	    0                & \text{if } n \in \{0, 1\} \\
	    \min (f(n-1) + C^{(n-1)}, f(n-2) + C^{(n-2)})  & \text{otherwise}
	\end{dcases}
```
2. Let $f(n)$ be the min cost to jump from step $n$, the recurrence can be defined as below,
```math
	f(n)= 
	\begin{dcases}
	    C^{n}                & \text{if } n \in \{0, 1\} \\
	    C^{n} + \min (f(n-1), f(n-2))  & \text{otherwise}
	\end{dcases}
```
where $C^i$ denotes the cost of $i$-th step.

[![746.png](https://i.postimg.cc/m2jmtSB2/746.png)](https://postimg.cc/56HBrLXZ)
#### 1. Recursive (TLE)
The most naive solution is to use a simple recursive function, which involves many duplicated computations.
```python
def minCostClimbingStairs(cost: List[int]) -> int:
    def _min_cost_to_step(n: int) -> int:
        if n in {0, 1}:
            return 0

        min_cost = min(
            _min_cost_to_step(n-1) + cost[n-1],
            _min_cost_to_step(n-2) + cost[n-2],
        )

        return min_cost

    n_steps = len(cost)
    return _min_cost_to_step(n_steps)


def minCostClimbingStairs(cost: List[int]) -> int:
    def _dfs(n: int) -> int:
        """Return the min cost to arrive n-th step.
        
        n is 0-indexed.
        """
        if n == 1:
            return 0
        elif n == 2:
            return min(cost[0], cost[1])
        
        return min(_dfs(n-2) + cost[n-2], _dfs(n-1) + cost[n-1])
    
    return _dfs(len(cost))


def minCostClimbingStairs(cost: List[int]) -> int:
    def _min_cost_from_step(n: int) -> int:
        if n in {0, 1}:
            return cost[n]
        
        return cost[n] + min(_min_cost_from_step(n-1), _min_cost_from_step(n-2))

    n_steps = len(cost)
    min_cost = min(
        _min_cost_from_step(n_steps-1),
        _min_cost_from_step(n_steps-2)
    )

    return min_cost
```
* Time complexity: $O(2^n)$
	* The recurrence can be represented by a binary tree, which takes $O(2^n)$ to complete all computations.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
#### 2. Recursive with Memoization
To avoid **duplicated computations**, we use the memoization technique to cache results we've already derived.
```python
def minCostClimbingStairs(cost: List[int]) -> int:
    def _min_cost_to_step(n: int) -> int:
        if n in min_cost:
            return min_cost[n]

        min_cost[n] = min(
            _min_cost_to_step(n-1) + cost[n-1],
            _min_cost_to_step(n-2) + cost[n-2],
        )
        return min_cost[n]

    min_cost = {0: 0, 1: 0}
    n_steps = len(cost)
    
    return _min_cost_to_step(n_steps)


def minCostClimbingStairs(cost: List[int]) -> int:
    def _dfs(n: int) -> int:
        """Return the min cost to arrive n-th step.
        
        n is 0-indexed.
        """
        if n in memo:
            return memo[n]
        
        memo[n] = min(_dfs(n-2) + cost[n-2], _dfs(n-1) + cost[n-1])
        return memo[n]
    
    memo = {
        1: 0,
        2: min(cost[0], cost[1]),
    }
    return _dfs(len(cost))


def minCostClimbingStairs(cost: List[int]) -> int:
    def _min_cost_from_step(n: int) -> int:
        if n in {0, 1}:
            return cost[n]
        if n in min_cost:
            return min_cost[n]

        min_cost[n] = cost[n] + min(_min_cost_from_step(n-1), _min_cost_from_step(n-2))
        return min_cost[n]
   
    min_cost = {}
    n_steps = len(cost)
    min_cost = min(_min_cost_from_step(n_steps-1), _min_cost_from_step(n_steps-2))

    return min_cost 
```
* Time complexity: $O(n)$
	* Eliminating all duplicated computations results in $O(n)$ time complexity.
		* The binary tree structure becomes more linear.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
	* A hash map caching results that have been derived takes $O(n)$.
#### 3. 1d DP with DP Array
Recursion solves the problem from a **top-down** view (though the underlying logic is **bottom-up**, think about returning from the call stack). In addition to the **top-down** view, we can also use a direct **bottom-up** method to iteratively derive and fill the minimum cost for each step.
```python
def minCostClimbingStairs(cost: List[int]) -> int:
    n_steps = len(cost)
    min_cost = [0] * (n_steps + 1)
    for step in range(2, n_steps+1):
        min_cost[step] = min(
            min_cost[step-1] + cost[step-1],
            min_cost[step-2] + cost[step-2]
        )

    return min_cost[n_steps]


def minCostClimbingStairs(cost: List[int]) -> int:
    """Same as above, but explicitly set dp[2]."""
    dp = [0] * (len(cost) + 1)
    dp[2] = min(cost[0], cost[1])
    for i in range(3, len(cost)+1):
        dp[i] = min(
            dp[i-2] + cost[i-2],
            dp[i-1] + cost[i-1]
        )

    return dp[-1]


def minCostClimbingStairs(cost: List[int]) -> int:
    n_steps = len(cost)

    # Initialize dp array
    dp = [0] * n_steps
    dp[0] = cost[0]
    dp[1] = cost[1]

    for n in range(2, n_steps):
        dp[n] = cost[n] + min(dp[n-1], dp[n-2])

    return min(dp[n_steps-1], dp[n_steps-2])
```
* Time complexity: $O(n)$
	* Deriving the minimum cost for each step takes $O(1)$, hence $O(n)$ in total.
* Space complexity: $O(n)$
	* A dp array caching results that have been derived takes $O(n)$.
#### 4. 1d DP - Space Optimization
Observing the recurrence, we can find out that the minimum cost of the current step depends only on the previous two. Hence, we only need to track the previous two temporary results to further reduce the space complexity.

Finally, we can also consider overwriting `cost` array with the minimum cost value of each step.
```python
def minCostClimbingStairs(cost: List[int]) -> int:
    n_steps = len(cost)
    prev, cur = cost[0], cost[1]
    for n in range(2, n_steps):
        prev, cur = cur, cost[n] + min(prev, cur)

    return min(prev, cur)


def minCostClimbingStairs(cost: List[int]) -> int:
    prev, cur = 0, min(cost[0], cost[1])
    for i  in range(3, len(cost) + 1):
        prev, cur = cur, min(prev + cost[i-2], cur + cost[i-1])
    
    return cur


def minCostClimbingStairs(cost: List[int]) -> int:
    n_steps = len(cost)
    for n in range(2, n_steps):
        cost[n] = cost[n] + min(cost[n-1], cost[n-2])

    return min(cost[n_steps-1], cost[n_steps-2])
```
* Time complexity: $O(n)$
	* Deriving the minimum cost for each step takes $O(1)$, hence $O(n)$ in total.
* Space complexity: $O(1)$