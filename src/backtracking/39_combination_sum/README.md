# [39. Combination Sum](https://leetcode.com/problems/combination-sum/)
> Find all unique combinations of integers adding up to a target integer. The same number can be used with **replacement**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.
### Input
* `candidates`: An integer array.
	* `1 <= candidates.length <= 30`
	* `2 <= candidates[i] <= 40`
	* All elements of `candidates` are **distinct**.
* `target`: A target integer.
	* `1 <= target <= 40`
### Output
All unique combinations of integers adding up to `target`.
* The most combinations can be up to 150.
### Example
```
Input: candidates = [2, 3, 6, 7], target = 7
Output: [[2, 2, 3], [7]]
Explanation:
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
7 is a candidate, and 7 = 7.
These are the only two combinations.
```
### Idea
#### 1. Full DFS
The most intuitive solution is to **fully expand** the combination tree shown as follows. However, we can see that there exist many duplicated combinations leading to exactly the same sum value.<br>
To remove duplicates, we use `set` to **filter** the candidate **sorted** combinations.

[![39.png](https://i.postimg.cc/J49FvRcn/39.png)](https://postimg.cc/xc3gbDZD)

```python
def combinationSum(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(comb: List[int]) -> None:
        if sum(comb) > target:
            return
        elif sum(comb) == target:
            res.add(tuple(sorted(comb.copy())))
            return

        for num in candidates:
            comb.append(num)
            _dfs(comb)
            comb.pop()

    res = set()
    _dfs([])

    return [list(comb) for comb in res]
```
* Time complexity: $O(T * n^T) \approx O(n^T)$, where $n$ is the number of elements, and $T$ denotes the target.
	* The full expansion results in the N-ary tree with height $T$ if `min(candidates) == 1`.
	* Each copy operation takes $O(T)$, where $T$ can be viewed as a constant here.
* Space complexity (**aux**): $O(T)$
	* The depth of call stack takes $O(T)$.
#### 2. Backtracking - DFS
To avoid traversing duplicated combinations, we **never look back** in `candidate` list. The process can be summarized as follows,
1. Select a **starting point** for current combination.
2. **Enumerate** possible combinations by adding a new element to the current combination.
    * Elements to be added start from the **starting point**.
3. Determine if pruning can be done.
	* `sum(comb) > target`: Directly return.
	* `sum(comb) == target`: Record the combination and return.

[![39-2.png](https://i.postimg.cc/PfFxgTzG/39-2.png)](https://postimg.cc/s17sSFNK)

```python
def combinationSum(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(i: int, comb: List[int]) -> None:
        if sum(comb) > target:
            return
        if sum(comb) == target:
            res.append(comb.copy())
            return

        for j in range(i, len(candidates)):
            comb.append(candidates[j])
            _dfs(j, comb)
            comb.pop()

    res = []
    _dfs(0, [])

    return res
```
* Time complexity: $O(T * n^T) \approx O(n^T)$, where $n$ is the number of elements, and $T$ denotes the target.
	* The N-ary tree with height $T$ (if `min(candidates) == 1`) takes $O(n^T)$.
		* Many **prunings** are done to optimize.
	* Each copy operation takes $O(T)$, where $T$ can be viewed as a constant here.
* Space complexity (**aux**): $O(T)$
	* The depth of call stack takes $O(T)$.
##### Comment
* Some other techniques can be used to speed up the algorithm.
	* Instead of deriving `sum` on the fly, we track the current sum at each level in a running manner.
	* To avoid exploring a combination with the first element greater than `target`, we sort `candidates` first (which takes $O(30\ log\ 30) \approx O(1)$) and add another pruning condition in the `for` loop.
```python
def combinationSum(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(i: int, comb: List[int], cur_sum: int) -> None:
        if cur_sum > target:
            return
        if cur_sum == target:
            res.append(comb.copy())
            return

        for j in range(i, len(candidates)):
            if candidates[j] > target:
                break

            comb.append(candidates[j])
            _dfs(j, comb, cur_sum + candidates[j])
            comb.pop()

    candidates.sort()
    res = []
    _dfs(0, [], 0)

    return res
```