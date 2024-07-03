# [1046. Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)
> Smash stones and return the weight of the last remaining stone. If there are no stones left, return `0`.

The rule of smashing is summarized as follows:
* Choose the **heaviest two stones** with weights `x` and `y`, where `x <= y`.
	* If `x == y`, both stones are destroyed.
	* If `x != y`, the stone of weight `x` is destroyed, and the stone of weight `y` has new weight `y - x`.
### Input
* `stones`: An integer array representing the weights of stones.
	* `1 <= stones.length <= 30`
	* `1 <= stones[i] <= 1000`
### Output
 The weight of the last remaining stone if any, else  `0`.
### Example
```
Input: stones = [2, 7, 4, 1, 8, 1]
Output: 1
Explanation:
We combine 7 and 8 to get 1 so the array converts to [2, 4, 1, 1, 1] then,
we combine 2 and 4 to get 2 so the array converts to [2, 1, 1, 1] then,
we combine 2 and 1 to get 1 so the array converts to [1, 1, 1] then,
we combine 1 and 1 to get 0 so the array converts to [1] then that's the value of the last stone.
```
### Idea
#### 1. Brute-force - Sorting
Because we need to take the top-2 heaviest stones to smash every time, the most intuitive way is to sort `stones` first. If there exist at least two stones, we do the following operations,
1. Take the top-2 heaviest stones.
2. Smash them.
3. Insert the remaining stone if the weight after smashing is greater than 0.
```python
def lastStoneWeight(stones: List[int]) -> int:
    def _smash(stones: List[int]) -> List[int]:
        stones.sort()
        y = stones.pop()
        x = stones.pop()
        smashed = y - x
        if smashed != 0:
            stones.append(smashed)
        
        return smashed

    while len(stones) > 1:
        _smash(stones)

    if len(stones) == 1:
        return stones[0]
    else:
        return 0
```
* Time complexity: $O(n^2\ log\ n)$
	* Smashing all stones until only one is left takes $n$ iterations, each of which takes $O(n\ log\ n)$ to complete.
* Space complexity: $O(1)$
	* No auxiliary space is used.
##### Comment
* The time complexity can be reduced to $O(n^2)$ if we just insert the remaining smashed stone to the correct position, instead of sorting `stones` for each iteration.
#### 2. Max Heap
To retrieve the maximum values from a list, we can leverage the heap property, which can reduce the time complexity to the log scale. The process is summarized as follows,
1. Heapify `stones`.
2. Pop the top-2 heaviest stones from the heap.
3. Smash them.
4. Pushe the remaining stone if the weight after smashing is greater than 0.

To use the built-in data structure in python, we negate the weights first and use min heap instead.
```python
def lastStoneWeight(stones: List[int]) -> int:
    stones = list(map(lambda wt: -1 * wt, stones))
    heapq.heapify(stones)
    while len(stones) > 1:
        y = heapq.heappop(stones)
        x = heapq.heappop(stones)
        smashed = y - x
        if smashed != 0:
            heapq.heappush(stones, smashed)

    if len(stones) == 1:
        return -1 * stones[0]
    else:
        return 0
```
* Time complexity: $O(n\ log\ n)$
	* Smashing all stones until only one is left takes $n$ iterations, each of which takes $O(3\ log\ n) \approx O(log\ n)$ to complete.
* Space complexity: $O(1)$
	* No auxiliary space is used.