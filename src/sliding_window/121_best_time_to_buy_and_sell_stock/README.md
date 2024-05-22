# [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
> Maximize the profit by selecting a day to buy one stock and sell on another day. Note that buy must be prior to sell.
### Input
* `prices`: An integer array containing prices of a stock on each day.
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
    n_days = len(prices)
    max_profit = 0
    for bd in range(n_days - 1):
        for sd in range(bd + 1, n_days):
            profit = prices[sd] - prices[bd]
            if profit > max_profit:
                max_profit = profit

    return max_profit
```
* Time complexity: $O(n^2)$
	* Deriving profits for all buy and sell pairs takes $O(n^2)$.
* Space complexity: $O(1)$
#### 2. DP Array
Instead of deriving profits for all possible buy and sell pairs, we observe that the profit can be **cumulative**. Let $f(d)$ be the maximum profit till day $d$, we can define the following recurrence,
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
	* A dp array tracking the maximum profit till each day takes $O(n)$.
#### 3. Kadane's Algorithm - DP w/ Space Optimization
Observing the recurrence defined in idea 2, we know that the maximum profit till day $d$ depends only on the information of days $d$ and $d-1$. Hence, we can further optimize space complexity by using two variables only,
1. `max_till_d`: The maximum profit till day $d$.
2. `max_profit`: The global maximum profit.
<br>
[![121-2-drawio.png](https://i.postimg.cc/RV1sFhWH/121-2-drawio.png)](https://postimg.cc/PPxzVtzX)
```python
def maxProfit(prices: List[int]) -> int:
    max_profit = 0 
    max_till_d = 0
    for d in range(1, len(prices)):
        max_till_d = max(max_till_d + (prices[d] - prices[d-1]), 0)
        max_profit = max(max_profit, max_till_d)

    return max_profit
```
* Time complexity: $O(n)$
	* Each day is visited only once.
* Space complexity: $O(1)$
#### 4. Two Pointers - Lowest Price Anchor
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
```
* Time complexity: $O(n)$
	* Each day is visited once.
* Space complexity: $O(1)$
#### 5. More DP
* https://labuladong.online/algo/dynamic-programming/stock-problem-summary/
### References
* [Largest Sum Contiguous Subarray (Kadane’s Algorithm)](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)