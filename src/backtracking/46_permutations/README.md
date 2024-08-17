# [46. Permutations](https://leetcode.com/problems/permutations/)
> Return all possible permutations of an integer array.

* [ ] Correctly analyze time complexity.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 6`
	* `-10 <= nums[i] <= 10`
	* All the integers of `nums` are **unique**.
### Output
All permutations of the array.
### Example
```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```
### Idea
All possible permutations can be visualized with a fully expanded tree structure with a constraint that no duplicated number can be included in the current permutation.
#### 1. DFS
Each time we go down one level, we have **one less number** to use because it's included in the current permutation. The process can be summarized as follows:
1. Fix the start point for exploration as 0.
	* We explore all possible numbers.
2. Determine if the number has been used or not.
	* **Prune** branches that lead to a permutation with any duplicated number.
3. Record the permutation if its length is equal to `len(nums)`.
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
	* The fully expanded tree has the number of the function calls no more than $O(n * n!)$.
		* The fully expanded tree has nodes less than a 2D grid with height of $n$ and width of $n!$.
	* Checking if an element is visited in the current permutation (a list) takes $O(n)$.
	* Each copy operation takes $O(n)$ at the last level (with $n!$ function calls).
* Space complexity: $O(n)$
	* The output list take $O(n * n!)$.
	* The depth of call stack takes $O(n)$, where $n$ is the number of elements in `nums`.
##### Comment
* To avoid $O(n)$ checks for existing elements in the current permutation, we can use an auxiliary data structure (e.g., a set (`add`/`remove`), a boolean array (`True`/`False`)) to help track visited elements.
	* Using add operator for adding a new number to `perm` needs to perform copy for each `_dfs` call.
```python
def permute(nums: List[int]) -> List[List[int]]:
    def _dfs(perm: List[int], seen: Set[int]) -> None:
        if len(perm) == len(nums):
            res.append(perm.copy())
            return

        for num in nums:
            if num not in seen:
                perm.append(num)
                seen.add(num)
                _dfs(perm, seen)

                # Restore the original state
                perm.pop()
                seen.remove(num)

    res = []
    _dfs([], set())

    return res


def permute(nums: List[int]) -> List[List[int]]:
    def _dfs(perm: List[int], seen: List[bool]) -> None:
        if len(perm) == len(nums):
            res.append(perm.copy())
            return

        for i, num in enumerate(nums):
            if not seen[i]:
                perm.append(num)
                seen[i] = True
                _dfs(perm + [num], seen)

                # Restore the original state
                perm.pop()
                seen[i] = False

    res = []
    seen = [False] * len(nums)
    _dfs([], seen)

    return res
```
#### 2. DFS with Swapping
The permutation can be though of as a process of **continually selecting an unseen element**. At each level, we swap an element to a position representing the **current choice**. For example, we select the first element at the first level, meaning we swap any number of `nums` to the first position. Then, we fix this first position, and swap any unseen number to the second position at the next level. The process stops when we hit the last position, meaning no swap is needed.
```python
def permute(nums: List[int]) -> List[List[int]]:
    def _dfs(i: int) -> None:
        if i == len(nums) - 1:
            # Swap the last position, meaning no swap is needed
            res.append(nums.copy())
            return

        for j in range(i, len(nums)):
            nums[i], nums[j] = nums[j], nums[i]
            _dfs(i + 1)
            nums[i], nums[j] = nums[j], nums[i]

    res = []
    _dfs(0)

    return res
```
* Time complexity: $O(n * n!)$
	* The fully expanded tree has the number of the function calls no more than $O(n * n!)$.
	* Each copy operation takes $O(n)$ at the last level (with $n!$ function calls).
* Space complexity: $O(n)$
	* The output list take $O(n * n!)$.
	* The depth of call stack takes $O(n)$, where $n$ is the number of elements in `nums`.