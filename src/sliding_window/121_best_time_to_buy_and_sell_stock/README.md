# [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
> Maximize the profit by selecting a day to buy one stock and sell on another day. Note that buy must be prior to sell.
### Input
* `prices`: An integer array containing the price of a stock on each day.
	* `1 <= prices.length <= 10^5`
	* `0 <= prices[i] <= 10^4`
### Output
The maximum profit if any profit can be made else `0`.
### Example
```
Input: prices = [7, 1, 5, 3, 6, 4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```
### Idea
#### 1. Brute-force (TLE)
The most intuitive solution is to derive profits for all possible buy and sell pairs and keep updating the maximum profit if the current pair is more profitable.
```python
def maxProfit(prices: List[int]) -> int:
    max_profit = 0
    for bd, bp in enumerate(prices):
        for sd in range(bd+1, len(prices)):
            cur_profit = prices[sd] - bp
            if cur_profit > max_profit:
                max_profit = cur_profit

    return max_profit
```
* Time complexity: $O(n^2)$
	* Deriving profits for all buy and sell pairs in a nested loop takes $O(n^2)$.
* Space complexity: $O(1)$
#### 2. Two Pointers - Pivot the Lowest Price
Fix the lowest price pointer to explore the highest profit within each interval.<br>
[![121-best-time-to-buy-and-sell-stock-drawio.png](https://i.postimg.cc/CKpJw4gF/121-best-time-to-buy-and-sell-stock-drawio.png)](https://postimg.cc/QFScb1CP)

```python
def maxProfit(prices: List[int]) -> int:
    lowest = 0 
    max_profit = 0
    for d in range(1, len(prices)):
        cur_profit = prices[d] - prices[lowest]
        if prices[d] < prices[lowest]:
            # Explore potentially higher profit
            lowest = d
        else:
            # Update if it's profitable
            max_profit = max(max_profit, cur_profit)
    
    return max_profit


def maxProfit(prices: List[int]) -> int:
    max_profit, min_price = 0, prices[0]
    for d in range(1, len(prices)):
        if prices[d] < min_price:
            min_price = prices[d]
        elif prices[d] - min_price > max_profit:
            max_profit = prices[d] - min_price

    return max_profit
```
* Time complexity: $O(n)$
	* Visiting each price at most once takes $O(n)$.
* Space complexity: $O(1)$
#### 3. Kadane's Algorithm - DP w/ Space Optimization
> But, I don't think of it as DP at the first glance.

Idea 2 is like finding the maximum profit **for each interval** and keep updating the global max profit. From a different point of view, we can instead consider **calculating the max cumulative profit** that can be made till each day $d$. For each day, we need to check,
1. If the cumulative profit till $d$ is negative, then we need to start from 0.
2. If the cumulative profit till $d$ is greater than the global max profit, we update the global max profit.

[![121-2-drawio.png](https://i.postimg.cc/mkfvqYV7/121-2-drawio.png)](https://postimg.cc/Hczv87qL)

```python
def maxProfit(prices: List[int]) -> int:
    max_profit, acc_profit = 0, 0
    for d in range(1, len(prices)):
        acc_profit = max(0, acc_profit + (prices[d] - prices[d-1]))
        max_profit = max(max_profit, acc_profit)

    return max_profit
```
* Time complexity: $O(n)$
	* Visiting each price at most once takes $O(n)$.
* Space complexity: $O(1)$
### Discussion
* Think how to solve this problem using the concept of DP. See [here](https://labuladong.online/algo/dynamic-programming/stock-problem-summary/).

%% Instead of deriving profits for all possible buy and sell pairs, we observe that the profit can be **cumulative**. Let $f(d)$ be the maximum profit till day $d$, we can define the following recurrence,
```math
	f(d) = 
	\begin{cases}
	    0,& \text{if } d = 1 \\
	    \max(f(d-1) + (p_d - p_{d-1}),\ 0),& \text{otherwise}
	\end{cases}
```
, where $p_d$ and $p_{d-1}$ are prices on day $d$ and $d-1$.<br>
During linear scan, we keep deriving **the maximum profit till current day** $d$. Finally, another linear scan is run to find the global maximum profit.
```python
def maxProfit(prices: List[int]) -> int:
    dp = [0] * len(prices)
    for d in range(1, len(prices)):
        profit_till_d = dp[d-1] + (prices[d] - prices[d-1])
        dp[d] = max(0, profit_till_d)
    max_profit = max(dp)

    return max_profit
```
* Time complexity: $O(n)$
	* Each day is visited only once.
	* Deriving the maximum profit takes $O(n)$.
* Space complexity: $O(n)$
	* A dp array tracking the maximum profit till each day takes $O(n)$. %%
### References
* [Largest Sum Contiguous Subarray (Kadane’s Algorithm)](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)
