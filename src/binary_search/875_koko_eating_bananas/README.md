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
#### 1. Brute-force (TLE)
The most intuitive solution is to try all possible eating speeds **from the slowest** (*i.e.,* 1 banana per hour) **to the fastest** (*i.e.,* the most bananas per hour). Then, we can guarantee the first speed that the koko can finish all bananas is the minimum one.
```python
def minEatingSpeed(piles: List[int], h: int) -> int:
    n_bananas_max = max(piles)
    for k in range(1, n_bananas_max + 1):
        n_hrs = 0
        for pile in piles:
            n_hrs += (pile - 1) // k + 1

        if n_hrs <= h:
            return k
```
* Time complexity: $O(mn)$, where $m$ is the number of bananas in the highest pile and $n$ is the number of piles.
	* Finding the pile with the most bananas takes $O(n)$.
	* Trying from the slowest speed to the fastest one takes $O(mn)$.
* Space complexity: $O(1)$
#### 2. Binary Search on Speed
Instead of trying all possible speeds, we can use the concept of binary search. There are two conditions summarized as follows,
1. If the koko can finish all bananas with the current speed `mid`,
	1. Record the current speed as a candidate.
	2. Update the **right** pointer to **slow down** eating rate.
2. If the koko can't finish with the current speed `mid`, update the **left** pointer to **speed up** eating rate.
```python
def minEatingSpeed(piles: List[int], h: int) -> int:
    def _cal_time_to_finish(k: int) -> int:
        """Calculate the number of hours to finish all bananas."""
        n_hrs = 0
        for pile in piles:
            n_hrs += (pile - 1) // k + 1

        return n_hrs

    l, r = 1, max(piles)
    k = r
    while l <= r:
        # The middle element is the candidate k
        mid = (l + r) // 2

        n_hrs = _cal_time_to_finish(mid)
        if n_hrs <= h:
            k = mid
            r = mid - 1
        else:
            l = mid + 1

    return k


def minEatingSpeed(piles: List[int], h: int) -> int:
    l, r = 1, max(piles)
    k = r
    while l <= r:
        # Derive candidate k and the corresponding time to finish
        mid = (l + r) // 2
        n_hrs = 0
        for pile in piles:
            n_hrs += (pile - 1) // mid + 1

        if n_hrs <= h:
            k = mid
            r = mid - 1
        else:
            l = mid + 1

    return k
```
* Time complexity: $O(n\ log\ m)$, where $m$ is the number of bananas in the highest pile and $n$ is the number of piles.
	* Finding the pile with the most bananas takes $O(n)$.
	* Running binary search on possible speeds helps reduce time complexity from $O(mn)$ to $O(n\ log\ m)$.
* Space complexity: $O(1)$