# [90. Subsets II](https://leetcode.com/problems/subsets-ii/)
> Return the power set of an integer array that may contain duplicates. The solution set can be returned in **any order** but **must not** contain duplicate subsets 
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 10`
	* `-10 <= nums[i] <= 10`
### Output
The power set of an integer array.
### Example
```
Input: nums = [1, 2, 2]
Output: [[], [1], [1, 2], [1, 2, 2], [2], [2, 2]]
```
### Idea
The idea to solve this problem is summarized in the following figure. We will discuss each solution from the most intuitive to the optimized one step by step.

[![90.png](https://i.postimg.cc/3RkvKzF8/90.png)](https://postimg.cc/JDWnx6Fv)
#### 1.  Full DFS
The most intuitive solution is to consider **to add or not to add** for each element in `nums` at each level. This solution results in a fully expanded binary tree with the depth of `len(nums)`.
```python
def subsetsWithDup(nums: List[int]) -> List[List[int]]:
    def _dfs(i: int, subset: List[int]) -> None:
        res.add(tuple(sorted(subset.copy())))
        if i == len(nums):
            return
            
        subset.append(nums[i])
        _dfs(i + 1, subset)
        subset.pop()
        _dfs(i + 1, subset)

    res = set()
    _dfs(0, [])

    return [list(subset) for subset in res]
```
* Time complexity: $O(n\ log\ n * 2^n)$
	* The number of function calls in the fully expanded binary tree is bounded by $O(2^n)$.
	* Sorting each candidate subset takes $O(n\ log\ n)$.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(2^n)$
	* The result list storing the power set takes $O(2^n)$.
	* The depth of call stack takes $O(n)$.
#### 2. Backtracking - DFS
To avoid duplicated candidate subset generation, we impose a restriction on the elements to add at the next level. To be concrete, it's about the choice of the **start point** in `nums`. This time, we use a **never-lookback** policy, which considers only elements **after** the current visited one as elements to add.
```python

def subsetsWithDup(nums: List[int]) -> List[List[int]]:
    def _dfs(start: int, subset: List[int]) -> None:
        res.add(tuple(sorted(subset.copy())))
        if start == len(nums):
            return
            
        for j in range(start, len(nums)):
            subset.append(nums[j])
            _dfs(j + 1, subset)
            subset.pop()
        
    res = set()
    _dfs(0, [])

    return [list(subset) for subset in res]
```
* Time complexity: $O(n\ log\ n * 2^n)$
	* The number of function calls in this binary tree is exactly $2^n$.
	* Sorting each candidate subset takes $O(n\ log\ n)$.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(2^n)$
	* The result list storing the power set takes $O(2^n)$.
	* The depth of call stack takes $O(n)$.
#### 3. Backtracking - Optimized DFS
As shown in the figure above, we can observe that there still exist **duplicated branches** that can be eliminated to reduce computational cost. The reason why these branches exist is that `nums` contains duplicated elements. The solution is quite intuitive. We only need to skip the duplicated elements and take unique ones into consideration.<br>
Take the second level of the left-most branch as an example. The current subset is `[1]`, and we consider to add `{2, 2, 3}` originally because the start point in `nums` is set to `1`. However, we can safely skip the second `2` (with index `2`) and let the first one (with index `1`) take care of all subsets starting with the number `2`. Hence, the duplicated branches can be eliminated, as marked in gray.<br>
To enable duplicated element checking, we sort `nums` in the beginning.
```python
def subsetsWithDup(nums: List[int]) -> List[List[int]]:
    def _dfs(start: int, subset: List[int]) -> None:
        res.append(subset.copy())
        if start == len(nums):
            return
            
        for j in range(start, len(nums)):
            if j > start and nums[j] == nums[j-1]:
                continue

            subset.append(nums[j])
            _dfs(j + 1, subset)
            subset.pop()
        
    nums.sort()
    res = []
    _dfs(0, [])

    return res
```
* Time complexity: $O(n * 2^n)$
	* Sorting `nums` takes $O(n\ log\ n)$.
	* The number of function calls in this binary tree is at most $2^n$.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(2^n)$
	* The result list storing the power set takes $O(2^n)$.
	* The depth of call stack takes $O(n)$.
#### 4. BFS
