# [70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)
> Calculate the total number of ways to climb to the top of a staircase. You can climb `1` or `2` steps each time.
### Input
* `n`: Number of steps of a staircase.
	* `1 <= n <= 45`
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
We first observe that this problem can be divided into many sub-problems by a **recurrence relationship**. Let $f(n)$ be the number of unique ways to climb a stair with steps $n$, we define the recurrence function as follows,
```math
	f(n)= 
	\begin{dcases}
	    1                & \text{if } n = 1 \\
	    2                & \text{if } n = 2 \\
	    f(n-1) + f(n-2)  & \text{otherwise}
	\end{dcases}
```
Hence, the key is that the current number can be determined by the previous two solutions!

[![70-new.png](https://i.postimg.cc/DygJk04k/70-new.png)](https://postimg.cc/F7Ys09xD)
#### 1. Recursive (TLE)
The most naive solution is to write a recursive function to continuously solve the question with a **divide-and-conquer** paradigm. It's obvious the base cases are `n == 1 or n == 2`. However, there are many overlapping sub-problems, which leads to an overhead of **duplicated computations**.

[![70-climbing-stairs-drawio.png](https://i.postimg.cc/Z5Kc3NPn/70-climbing-stairs-drawio.png)](https://postimg.cc/6ygnxymx)]

```python
def climbStairs(n: int) -> int:
    # Base cases
    if n in {1, 2}:
        return n
    
    return climbStairs(n-1) + climbStairs(n-2)
```
* Time complexity: $O(2^n)$
	* The recursively break-down sub-problems can be represented by a binary tree, which can take $O(2^n)$ to traverse.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
#### 2. Recursive with Memoization
To avoid duplicated computations, we can incorporate the technique of **memoization** into our solution. The core idea can be summarized in three steps, including retrieving (read), deriving, and storing (write).

[![70-climbing-stairs-memo-drawio.png](https://i.postimg.cc/7Lyzvswf/70-climbing-stairs-memo-drawio.png)](https://postimg.cc/k2fGKyVm)

```python
def climbStairs(n: int) -> int:
    def _climbStairs(n: int)-> int:
        if n in memo:
            return memo[n]
        
        memo[n] = _climbStairs(n-1) + _climbStairs(n-2)
        return memo[n]
    
    memo = {1: 1, 2: 2}
    return _climbStairs(n)
```
* Time complexity: $O(n)$
	* As duplicated computations are replaced with direct retrievals from `memo`, the time complexity drops to $O(n)$.
		* Observing the tree, it becomes linear with memoization.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
#### 3.  1d DP with DP Array
Next, we can also solve this problem in a **bottom-up** manner, which iteratively derives and fills the answer of the next step based on the previous two.
```python
def climbStairs(n: int) -> int:
    dp = [0] * (n + 1)
    for i in range(n+1):
        if i in {0, 1}:
            dp[i] = 1
        else:
            dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]


def climbStairs(n: int) -> int:
    dp = [1] * (n + 1)
    for i in range(2, n+1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
```
* Time complexity: $O(n)$
	* Deriving and filling the solution for each step takes $O(1)$, hence $O(n)$ for all $n$ steps.
* Space complexity: $O(n)$
	* An 1d array storing the solution for each step takes $O(n)$.
#### 4. Two Pointers for Space Optimization
As defined in the recurrence relationship, the current solution can be determined by considering only the previous two solutions. Hence, we adopt two pointers to store these two temporary solutions on the fly.
```python
def climbStairs(n: int) -> int:
    n_2, n_1 = 1, 1  # Record n-2 and n-1
    for i in range(2, n+1):
        tmp = n_1
        n_1 = n_2 + n_1
        n_2 = tmp

    return n_1

def climbStairs(n: int) -> int:
    prev, cur = 1, 1
    for _ in range(2, n+1):
        prev, cur = cur, prev + cur

    return cur
```
* Time complexity: $O(n)$
	* Deriving the solution for each step takes $O(1)$, hence $O(n)$ for all $n$ steps.
* Space complexity: $O(1)$