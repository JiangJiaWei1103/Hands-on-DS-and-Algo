# [90. Subsets II](https://leetcode.com/problems/subsets-ii/)
> Return the power set of an integer array that may contain duplicates. The solution set can be returned in **any order** but **must not** contain duplicate subsets.

* [ ] Think about the binary view (take or not take) and solve in both iterative and recursive.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 10`
	* `-10 <= nums[i] <= 10`
### Output
The power set of an integer array.
### Example
```
Input: nums = [1,2,2]
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]
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
		* $\sum_{i=1}^{n} 2^i = 2 + 2^2 + ... + 2^n = 2(2^n - 1)$
	* Sorting each candidate subset takes $O(n\ log\ n)$.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
	* The result list storing the power set takes $O(n * 2^n)$.
#### 2. Backtracking DFS
To avoid duplicated candidate subset generation, we redraw the tree structure (i.e., a different way to explore potential subsets). Suppose we are now on the $l$-th level, we break down the problem by considering:
1. Breadth: Which number can be used to start a new branch on the $l$-th level?
2. Depth: Which number can be used as a start point to extend the exploration of a specific branch on the $(l+1)$-th level?

In this solution, we use a **never-lookback** policy, which considers only elements **after** the newly added number, namely `nums[j]` on the next level. That is, the start point is set to `j + 1`. Note that we consider only the second point in this solution. The first point will be solved in the idea 3.
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
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
	* The result list storing the power set takes $O(n * 2^n)$.
#### 3. Optimized Backtracking DFS
In the idea 2, we still need to check and remove duplicated subsets. The reason is that `nums` itself contains duplicated numbers. To solve this problem, we consider the first point stated in the idea 2. Specifically, we limit potential branches to explore by starting a new branch with an unique number.

Take the second level of the left-most branch as an example. The current subset is `[1]`, and we consider to add `{2, 2, 3}` originally because the start point on this level is set to `1`. However, we can safely skip the second `2` (with index `2`) and let the first one (with index `1`) take care of all subsets starting with the number `2`. Hence, the duplicated branches can be eliminated, as marked in gray.

In short, the key points are summarized as follows:
1. Sort `nums` first to group unique numbers together.
2. Start a new branch using the unique numbers.
	* The **start point** on that level.
	* The **first occurrence** of each unique number 
```python
def subsetsWithDup(nums: List[int]) -> List[List[int]]:
    def _dfs(subset: List[int], i: int) -> None:
        res.append(subset.copy())

        for j in range(i, len(nums)):
            if j > i and nums[j-1] == nums[j]:
                continue
            
            subset.append(nums[j])
            _dfs(subset, j+1)
            subset.pop()

    nums.sort()
    res = []
    _dfs([], 0)

    return res
```
* Time complexity: $O(n * 2^n)$
	* Sorting `nums` takes $O(n\ log\ n)$.
	* The number of function calls in this binary tree is at most $2^n$ when there's no duplications in `nums`.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
	* The result list storing the power set takes $O(n * 2^n)$.
#### 4. Iterative
This solution can be visualized from a binary view (i.e., take it or not take).
```python
def subsetsWithDup(nums: List[int]) -> List[List[int]]:
    nums.sort()
    res = [[]]
    size = 0
    for i in range(len(nums)):
        j = size if i > 0 and nums[i-1] == nums[i] else 0
        size = len(res)
        while j < size:
            subset = res[j].copy()
            subset.append(nums[i])
            res.append(subset)
            j += 1

    return res
```
