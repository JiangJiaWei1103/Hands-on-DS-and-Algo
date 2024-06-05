# [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)
> Find all unique combinations of numbers adding up to a target integer. Each number can only be used **once** and the solution must not contain duplicated combinations.
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
Input: candidates = [10, 1, 2, 7, 6, 1, 5], target = 8
Output:
[
	[1, 1, 6],
	[1, 2, 5],
	[1, 7],
	[2, 6]
]
```
### Idea
As we practice many similar questions, we might have already found a general paradigm to solve this kind of question. The core idea can be summarized as follows,
1. Find a **start point** to explore at the **next** level.
	* Prune the search space horizontally (breath).
2. At each level, explore the search space **with restrictions**.
	* Determine whether to explore all latter elements.
		* Prune the search space horizontally, more like **early stopping**.
			* *e.g.,* the current sum is greater than `target`
	* Determine whether to skip the current visited element.
		* Prune the search space horizontally.
			* *e.g.,* the current visited element is a **duplicated** one.
3. Each single exploration involves,
	* Visit a new element.
	* Step to the next level.
	* **Backtrack**, restore the original status.
#### 1. Backtracking - DFS
```python
def combinationSum2(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(start: int, comb: List[int], cur_sum: int) -> None:
        if cur_sum == target:
            res.append(comb.copy())
            return

        for j in range(start, len(candidates)):
            if cur_sum + candidates[j] > target:
                # No need to explore greater elements
                break
            if j > start and candidates[j] == candidates[j-1]:
                # Skip the duplicated element
                continue
            
            comb.append(candidates[j])
            _dfs(j + 1, comb, cur_sum + candidates[j])
            comb.pop()

    candidates.sort()
    res = []
    _dfs(0, [], 0)

    return res


def combinationSum2(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(start: int, comb: List[int], cur_sum: int) -> None:
        if cur_sum == target:
            res.append(comb.copy())
            return

        for j in range(start, len(candidates)):
            if cur_sum + candidates[j] > target:
                break

            if j == start or candidates[j] != candidates[j-1]:
                comb.append(candidates[j])
                _dfs(j + 1, comb, cur_sum + candidates[j])
                comb.pop()

    candidates.sort()
    res = []
    _dfs(0, [], 0)

    return res
```
* Time complexity: $O(n * 2^n)$
	* The number of function calls in the N-ary tree is exactly $2^n$.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(n * 2^n)$
	* The result list storing all possible combinations takes $O(n * 2^n)$.
	* The depth of call stack takes $O(n)$.
