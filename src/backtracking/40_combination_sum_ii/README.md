# [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)
> Find all unique combinations of numbers adding up to a target integer. Each number can only be used **once** and the solution must not contain duplicated combinations.

* [ ] Study DP solutions.
### Input
* `candidates`: An integer array.
	* `1 <= candidates.length <= 100`
	* `1 <= candidates[i] <= 50`
* `target`: A target integer.
	* `1 <= target <= 30`
### Output
All combinations of numbers adding up to `target`.
### Example
```
Input: candidates = [10,1,2,7,6,1,5], target = 8
Output: 
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```
### Idea
As we practice many similar questions, we might have already found a general paradigm to solve this kind of question. Suppose the search space can be represented as a **N-ary** tree and we're on the $l$-th level, the core idea can be summarized as follows:
1. Start exploration from a **start point** for the current level.
	* Prune the search space horizontally (e.g., use one element exactly once and never look back).
```python
# i is the start point
for j in range(i, len(nums)):
	# ...
```
2. Keep branching on the $l$-th level with constraints (i.e., prune the search space horizontally).
	* Early stop.
		* e.g., the number is greater than the target sum.
	* Unique branching.
		* e.g., avoid unique combinations.
3. For each branch, we do:
	* Add the current visited number.
	* Go to the $(l+1)$-th level with the updated objects (e.g., subsets, combinations) and a new **start point**.
	* **Backtrack**: Remove or pop the newly added number to restore the original state.

Finally, we sometimes sort the array in the beginning to avoid duplications in the result.
#### 1. Backtracking DFS
```python
def combinationSum2(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(comb: List[str], i: int, cur_sum: int) -> None:
        if cur_sum > target:
            return
        if cur_sum == target:
            res.append(comb.copy())
            return

        for j in range(i, len(candidates)):
            if candidates[j] > target:
                # No need to explore the following numbers
                break
            elif j > i and candidates[j-1] == candidates[j]:
                # Skip branches that can lead to duplications
                continue

            comb.append(candidates[j])
            _dfs(comb, j + 1, cur_sum + candidates[j])
            comb.pop()

    candidates.sort()
    res = []
    _dfs([], 0, 0)

    return res


def combinationSum2(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(comb: List[str], i: int, cur_sum: int) -> None:
        if cur_sum == target:
            res.append(comb.copy())
            return

        for j in range(i, len(candidates)):
            if cur_sum + candidates[j] > target:
                # No need to explore the following numbers
                # and avoid an unnecessary function call
                break
            elif j > i and candidates[j-1] == candidates[j]:
                # Skip branches that can lead to duplications
                continue

            comb.append(candidates[j])
            _dfs(comb, j + 1, cur_sum + candidates[j])
            comb.pop()

    candidates.sort()
    res = []
    _dfs([], 0, 0)

    return res
```
* Time complexity: $O(n * 2^n)$
	* The number of function calls in the N-ary tree is bound by $2^n$.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
	* The result list storing all possible combinations is bounded by $O(n * 2^n)$.
##### Comment
* With the same logic, we can convert to the BFS implementation.
```python
def combinationSum2(candidates: List[int], target: int) -> List[List[int]]:
    candidates = [num for num in sorted(candidates) if num <= target]

    res = []
    q = deque([([], 0)])
    while len(q) > 0:
        comb, i = q.popleft()  # Current comb, start point
        for j in range(i, len(candidates)):
            cur_sum = sum(comb) + candidates[j]
            if cur_sum > target:
                break
            elif cur_sum == target:
                comb.append(candidates[j])
                res.append(comb)
                break

            if j == i or candidates[j-1] != candidates[j]:
                # Copy here, or combs in queue will be affected
                q.append((comb + [candidates[j]], j+1))

    return res
```
#### 2. DP
* https://leetcode.com/problems/combination-sum-ii/solutions/16870/dp-solution-in-python/
