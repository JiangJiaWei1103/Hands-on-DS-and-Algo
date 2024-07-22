# [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)
> Find the minimum bananas-per-hour eating speed `k` such that a koko can finish all piles of bananas before the guards come back.
* If the pile has less than `k` bananas, she eats all of them instead and **will not eat any more bananas during this hour**.
### Input
* `piles`: An integer array containing the number of bananas per pile.
	* `1 <= piles.length <= 10^4`
	* `1 <= piles[i] <= 10^9`
* `h`: An integer indicating how many hours guards will be gone.
	* `piles.length <= h <= 10^9`
### Output
The minimum bananas-per-hour eating speed `k` such that the koko can finish all bananas.
### Example
```
Input: piles = [3, 6, 7, 11], h = 8
Output: 4
```
### Idea
There are some key points to keep in mind before solving this problem,
1. `h >= len(piles)`: If the number of piles is greater than the time limit, it's impossible for the koko to finish all piles.
2. `1 <= k <= max(piles)`: The eating rate `k` is bounded by `[1, max(piles)]`.
	* The slowest rate is one banana per hour, which takes `sum(piles)` to finish.
	* The fastest rate is `max(piles)` bananas per hour, which takes `len(piles)` to finish.
#### 1. Brute-force (TLE)
The most intuitive solution is to try all possible eating speeds **from the slowest** (*i.e.,* 1 banana per hour) **to the fastest** (*i.e.,* the maximum pile per hour). Then, we can guarantee the first speed that the koko can finish all bananas is the minimum one.
```python
def minEatingSpeed(piles: List[int], h: int) -> int:
    max_pile = max(piles)
    for k in range(1, max_pile+1):
        t = 0
        for pile in piles:
            t += (pile - 1) // k + 1  # or math.ceil(pile / k)

        if t <= h:
            return k
```
* Time complexity: $O(mn)$, where $m$ is the number of bananas in the maximum pile and $n$ is the number of piles.
	* Trying each eating rate from the slowest to the fastest takes $O(m)$.
	* For each eating rate, calculating the total time to finish takes $O(n)$, hence $O(mn)$ in total.
* Space complexity: $O(1)$
#### 2. Binary Search on Speed
Instead of trying all possible speeds, we can use the concept of binary search. The process merely determines whether the koko should speedup or slowdown. There are two conditions,
1. If the koko **can** finish all bananas with the current speed `k`,
	1. Update `min_k`: Record the slower eating rate.
	2. Slowdown: Move **right** pointer to the left side of `k`.
2. If the koko **can't** finish with the current speed `k`, speedup by moving **left** pointer to the right side of `k`.
```python
def minEatingSpeed(piles: List[int], h: int) -> int:
    l, r = 1, max(piles)
    min_k = r  # Initialize with the fastest eating rate
    while l <= r:
        # Derive the time to finish all piles for the current rate k
        k = (l + r) // 2
        t = 0
        for pile in piles:
            t += (pile - 1) // k + 1  # or math.ceil(pile / k)
        
        if t <= h:
            min_k = min(min_k, k)
            r = k - 1
        else:
            l = k + 1
    
    return min_k 
```
* Time complexity: $O(n\ log\ m)$, where $m$ is the number of bananas in the highest pile and $n$ is the number of piles.
	* Running binary search on possible speeds helps reduce time complexity from $O(m)$ to $O(log\ m)$.
	* For each eating rate, calculating the total time to finish takes $O(n)$, hence $O(n\ log\ m)$ in total.
* Space complexity: $O(1)$