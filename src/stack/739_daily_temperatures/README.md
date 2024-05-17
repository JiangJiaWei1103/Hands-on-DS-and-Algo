# [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)
> Find the number of days you have to wait to get a warmer temperature for each day.
### Input
* `temperatures`: An integer array containing daily temperatures.
	* `1 <= temperatures.length <= 10^5`
	* `30 <= temperatures[i] <= 100`
### Output
A list recording the number of days you need to wait to get a warmer temperature.
### Example
```
Input: temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
Output: [1, 1, 4, 2, 1, 1, 0, 0]
Explanation: Take i == 2 (temperatures[2] == 75) for example, we need to wait 4 days to get a warmer temperature 76 on 6th day.
```
### Idea
#### 1. Brute-force (TLE)
The most intuitive solution is to run a **linear search** for each day until the temperature gets warmer.
```python
def dailyTemperatures(temperatures: List[int]) -> List[int]:
    n_days = len(temperatures)
    res = []
    for i in range(0, n_days):
        cnt = 1
        for j in range(i+1, n_days):
            if temperatures[j] > temperatures[i]:
                break
            cnt += 1
        res.append(cnt)

    # Fix those without future days
    for i in range(n_days-1, -1, -1):
        if -1 * (i - n_days) == res[i]:
            res[i] = 0
        
    return res
```
* Time complexity: $O(n^2)$
	* Running linear search for each day takes $O(n^2)$.
* Space complexity: $O(n)$
	* A list storing number of days per day takes $O(n)$.
#### 2. Stack
The concept of using a stack is that we need to track historical information, including temperatures and previous day indices, to
1. Determine whether the current temperature gets warmer.
2. Derive the number of past days by simply calculating index difference.
For comparison, we keep popping past days from the stack if the current temperature is warmer than theirs. Hence, the stack is **monotonically decreasing**.
```python
def dailyTemperatures(temperatures: List[int]) -> List[int]:
    res = [0] * len(temperatures)
    stack = []
    for d, cur_temp in enumerate(temperatures):
        while len(stack) > 0 and stack[-1][0] < cur_temp:
            _, prev_d = stack.pop()
            res[prev_d] = d - prev_d 

        stack.append((cur_temp, d))

    return res


def dailyTemperatures(temperatures: List[int]) -> List[int]:
    res = [0] * len(temperatures)
    stack = []
    for d, cur_temp in enumerate(temperatures):
        while len(stack) > 0 and temperatures[stack[-1]] < cur_temp:
            prev_d = stack.pop()
            res[prev_d] = d - prev_d 

        stack.append(d)

    return res
```
* Time complexity: $O(n)$
	* Each day is visited once.
* Space complexity: $O(n)$
	* A list storing number of days per day takes $O(n)$.
	* A stack storing past temperatures takes $O(n)$.