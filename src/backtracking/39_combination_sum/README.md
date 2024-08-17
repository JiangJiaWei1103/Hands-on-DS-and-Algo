# [39. Combination Sum](https://leetcode.com/problems/combination-sum/)
> Find all unique combinations of integers adding up to a target integer. The same number can be used with **replacement**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

* [ ] Correctly analyze time and space complexity.
* [ ] Try DP solutions.
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
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
Explanation:
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
7 is a candidate, and 7 = 7.
These are the only two combinations.
```
### Idea
Instead of enumerating all possible combinations, we can identify the constraint to early prune branches that won't lead to a valid answer. We consider the following two cases:
1. `sum(comb) == target`: The combination is already a valid answer, prune it.
2. `sum(comb) > target`: The combination can't lead to a valid answer, prune it.
#### 1. Full DFS
The most intuitive solution is to **fully expand** the combination tree shown as follows. However, we can see that there exist many **duplicated** combinations. To remove duplicates, we use `set` to **filter** the **sorted** candidate combinations.

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
* Space complexity: $O(T)$
	* The depth of call stack takes $O(T)$.
##### Comment
* Generally speaking, $T$ should be expressed as $\frac{T}{M}$, where $M$ is `min(candidates)`.
#### 2. Backtracking DFS
To avoid creating a branch leading to a duplicated combination, we make the constraint more strict by making sure that numbers to add on the next level never traverse backward in `candidates`. The process can be summarized as follows:
1. Select a **start point** of numbers to add for current combination.
	* Obey the never-look-back rule.
2. **Enumerate** possible combinations by adding a new element to the current combination.
    * From **start point** till the end of `candidates`.
3. Determine if pruning can be done.
	* `sum(comb) > target`: Directly return.
	* `sum(comb) == target`: Record the combination and return.

[![39-2.png](https://i.postimg.cc/PfFxgTzG/39-2.png)](https://postimg.cc/s17sSFNK)

```python
def combinationSum(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(comb: List[int], i: int) -> None:
        cur_sum = sum(comb)
        if cur_sum > target:
            return
        if cur_sum == target:
            res.append(comb.copy())
            return
        
        for j in range(i, len(candidates)):
            comb.append(candidates[j])
            _dfs(comb, j)
            comb.pop()

    res = []
    _dfs([], 0)
    
    return res
```
* Time complexity: $O(T * n^T) \approx O(n^T)$, where $n$ is the number of elements, and $T$ denotes the target.
	* The N-ary tree with height $T$ (if `min(candidates) == 1`) takes $O(n^T)$.
		* Many **prunings** are done to optimize.
	* Each copy operation takes $O(T)$, where $T$ can be viewed as a constant here.
* Space complexity: $O(T)$
	* The depth of call stack takes $O(T)$.
##### Comment
* Some other techniques can be used to speed up the algorithm.
	* Instead of deriving `sum` on the fly, we track the current sum at each level in a **running** manner.
	* To avoid exploring a combination with the first element greater than `target`, we sort `candidates` first (which takes $O(30\ log\ 30) \approx O(1)$) and add another pruning condition in the `for` loop.
```python
def combinationSum(candidates: List[int], target: int) -> List[List[int]]:
    def _dfs(comb: List[int], cur_sum: int, i: int) -> None:
        if cur_sum > target:
            return
        if cur_sum == target:
            res.append(comb)
            return
        
        for j in range(i, len(candidates)):
            if candidates[j] > target:
                break 

            _dfs(comb + [candidates[j]], cur_sum + candidates[j], j)

    candidates.sort()
    res = []
    _dfs([], 0)
    
    return res
```
* Convert to backtracking BFS. We keep pushing candidate combinations that might lead to a valid answer into the queue, which is just how **pruning** aims to achieve.
```python
def combinationSum(candidates: List[int], target: int) -> List[List[int]]:
    candidates.sort()
    candidates = [num for num in candidates if num <= target]

    res = []
    q = deque([([num], num) for num in candidates])
    while len(q) > 0:
        comb, cur_sum = q.popleft()
        if cur_sum > target:
            continue
        if cur_sum == target:
            res.append(comb)
            continue

        for num in candidates:
            if num >= comb[-1]:
                q.append((comb + [num], cur_sum + num))
    
    return res
```
#### 4. Dynamic Programming

### Observations
* Compared to the implementation using `push` and `pop`, the one using the add operator already returns a new list (i.e., copy behind the scene). Hence, no copy is needed when the answer is appended to `res`.