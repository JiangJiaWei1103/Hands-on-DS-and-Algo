# [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
> Maximize the profit by selecting a day to buy one stock and sell on another day. Note that buy must be prior to sell.
### Input
* `prices`: Prices of a stock on each day.
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
#### 1. Nested Loops
Check each buy/sell pairs from left to right and record the maximum profit.
```python
def maxProfit(prices: List[int]) -> int:
    n_days = len(prices)
    max_p = 0 
    for bd, bp in enumerate(prices):
        for sd in range(bd+1, n_days):
            cur_p = prices[sd] - bp
            if cur_p > max_p:
                max_p = cur_p

    return max_p
```
* Time complexity: $O(n^2)$
	* Check all possible buy/sell pairs.
* Space complexity: $O(1)$
#### 2. Two Pointers
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
#### 3. Kadane's Algorithm
Convert the problem to finding the interval (*i.e.,* contiguous subarray) having the highest accumulated profit (*i.e.,* largest value sum).<br>
[![121-2-drawio.png](https://i.postimg.cc/RV1sFhWH/121-2-drawio.png)](https://postimg.cc/PPxzVtzX)
```python
def maxProfit(prices: List[int]) -> int:
    acc_profit = 0  # Accumulated profit of the current interval
    max_profit = 0
    for d in range(1, len(prices)):
        acc_profit += prices[d] - prices[d-1]
        acc_profit = max(0, acc_profit)
        max_profit = max(max_profit, acc_profit)
    
    return max_profit
```
* Time complexity: $O(n)$
	* Each day is visited once.
* Space complexity: $O(1)$

### References
* [Largest Sum Contiguous Subarray (Kadane’s Algorithm)](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/)