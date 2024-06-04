# [46. Permutations](https://leetcode.com/problems/permutations/)
> Return all possible permutations of an integer array.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 6`
	* `-10 <= nums[i] <= 10`
	* All the integers of `nums` are **unique**.
### Output
All permutations of the array.
### Example
```
Input: nums = [1, 2, 3]
Output: [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]
```
### Idea
#### 1. DFS
All possible permutations can be visualized with a fully expanded tree structure. Each time we go down one level, we have **one less number** to used because it's included in the current permutation. The process can be summarized as follows,
1. Fix the start point for exploration as 0.
	* We explore all possible numbers.
2. Determine if the number has been used or not.
	* Prune the branch if `num in perm`.
3. Record the permutation if its length is equal to `len(nums)`
```python
def permute(nums: List[int]) -> List[List[int]]:
    def _dfs(perm: List[int]) -> None:
        if len(perm) == len(nums):
            res.append(perm.copy())
            return
        
        for num in nums:
            if num in perm:
                continue
            
            perm.append(num)
            _dfs(perm)
            perm.pop()

    res = []
    _dfs([])

    return res 
```
* Time complexity: $O(n * n!)$
	* The fully expanded tree has the number of the function calls bounded by $O(n!)$.
	* Checking if an element is visited in the current permutation (a list) takes $O(n)$.
	* Each copy operation takes $O(n)$.
* Space complexity: $O(n!)$
	* All permutations in the output take $O(n!)$.
##### Comment
* To avoid $O(n)$ checks for existing elements in the current permutation, we can use an auxiliary data structure (*e.g.,* a set, a boolean array) to help track visited elements.
```python
def permute(nums: List[int]) -> List[List[int]]:
    def _dfs(visited: List[bool], perm: List[int]) -> None:
        if len(perm) == len(nums):
            res.append(perm.copy())
            return
        
        for i, num in enumerate(nums):
            if visited[i]:
                continue
            
            visited[i] = True
            perm.append(num)
            _dfs(visited, perm)

            # Restore the status
            visited[i] = False
            perm.pop()

    res = []
    visited = [False] * len(nums)
    _dfs(visited, [])

    return res 
```
#### 2. Swapping