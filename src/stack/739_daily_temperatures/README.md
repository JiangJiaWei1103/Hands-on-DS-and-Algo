# [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)
> Find the number of days you have to wait to get a warmer temperature than today for each day.
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
    d = len(temperatures)
    res = [0] * d
    for i in range(d-1):
        cnt = 1 
        for j in range(i+1, d):
            if temperatures[j] > temperatures[i]:
                break
            cnt += 1

        res[i] = cnt

    # Fix those without future days
    for i in range(d):
        if res[i] == d - i:
            res[i] = 0

    return res
```
* Time complexity: $O(n^2)$
	* Running linear search for each day takes $O(n^2)$.
* Space complexity: $O(n)$
	* A list storing number of days to wait per day takes $O(n)$.
#### 2. Non-Increasing Stack
To derive the day difference between the current day and the future day, we can use a **non-increasing** (in terms of the temperature) stack to store the day index until a warmer day appears. For each visited day, we compare the temperature with the past days stored in the stack. There are two conditions,
1. If the visited temperature is warmer than the top of the stack, pop the past day from the stack and record the day difference.
2. If the visited temperature is cooler or equal to the top of the stack, push the current day into the stack.

In short, we **keep popping past days** from the stack if the visited temperature is warmer than theirs.
```python
def dailyTemperatures(temperatures: List[int]) -> List[int]:
    d = len(temperatures)
    res = [0] * d
    stack = []
    for i in range(d):
        while len(stack) > 0 and temperatures[i] > stack[-1][0]:
            res[stack[-1][1]] = i - stack[-1][1]
            stack.pop()
        stack.append((temperatures[i], i))

    return res


def dailyTemperatures(temperatures: List[int]) -> List[int]:
    res = [0] * len(temperatures)
    stack = []
    for d, t in enumerate(temperatures):
        while len(stack) > 0 and t > temperatures[stack[-1]]:
            hist_d = stack.pop()
            res[hist_d] = d - hist_d
        stack.append(d)

    return res
```
* Time complexity: $O(n)$
	* Each day is pushed to and popped from the stack at most once, which takes $O(n)$ to complete.
* Space complexity: $O(n)$
	* An output list storing number of days per day takes $O(n)$.
	* A stack storing historical day information takes $O(n)$.