# [70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
> Calculate the total number of ways to climb to the top of a staircase. You can climb `1` or `2` steps each time.
### Input
* `n`: The number of steps of a staircase.
### Output
The total number of distinct ways to reach the top.
### Example
```
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```
### Idea
#### 1. Recursive
Use the concept of Fibonacci numbers.
[![70-climbing-stairs-drawio.png](https://i.postimg.cc/Z5Kc3NPn/70-climbing-stairs-drawio.png)](https://postimg.cc/6ygnxymx)
```python
def climbStairs(n: int) -> int:
    # Base case
    if n == 1:
        return 1
    elif n == 2:
        return 2
    
    return climbStairs(n-1) + climbStairs(n-2)


def climbStairs(n: int) -> int:
    # Base case
    if n == 0 or n == 1:
        return 1
    
    return climbStairs(n-1) + climbStairs(n-2)
```
* Time complexity: $O(2^n)$
	* Have duplicated calculations.
* Space complexity: $O(n)$
	* The depth of the call stack will never exceed $n$.
#### 2. Recursion w/ Memoization
Map each step to the number of ways to avoid the redundant function calls and duplicated calculations.
[![70-climbing-stairs-memo-drawio.png](https://i.postimg.cc/7Lyzvswf/70-climbing-stairs-memo-drawio.png)](https://postimg.cc/k2fGKyVm)
```python
def climbStairs(n: int) -> int:
    def _climb(n, memo):
        if n in memo:
            return memo[n]

        memo[n] = _climb(n-1, memo) + _climb(n-2, memo)
        return memo[n]

    memo = {1: 1, 2: 2}
    return _climb(n, memo)
```
* Time complexity: $O(n)$
	* Direct retrieval avoids duplicated calculations.
* Space complexity: $O(n)$
	* The depth of the call stack will never exceed $n$.
#### 3. Dynamic Programming
Record the number of ways for each step in a table to eliminate duplicated calculations in a **bottom-up** manner iteratively.
```python
def climbStairs(n: int) -> int:
    if n == 1:
        return 1
    elif n == 2:
        return 2

    dp = [1, 2]
    for _ in range(n-2):
        dp.append(dp[-2] + dp[-1])

    return dp[-1]
```
* Time complexity: $O(n)$
	* Derive the number of ways to reach the top for each intermediate step.
* Space complexity: $O(n)$
	* Record the number of ways to reach the top for each intermediate step.
##### Comment
* Instead of maintaining the table, one can use two pointers to track the current sum to achieve $O(1)$ space complexity.