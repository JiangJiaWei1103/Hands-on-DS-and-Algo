# [78. Subsets](https://leetcode.com/problems/subsets/)
> Return the **power set** of an integer array. No duplicated subsets are allowed and the solution can be returned in any order.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 10`
	* `-10 <= nums[i] <= 10`
	* All the numbers of `nums` are **unique**.
### Output
### Example
```
Input: nums = [1, 2, 3]
Output: [[], [1], [2], [1, 2], [3], [1, 3], [2, 3], [1, 2, 3]]
```
### Idea
#### 1. Full DFS
The idea of generating the power set is to decide whether to add the visited number or not. That is, we can choose to add `nums[i]` or keep it as is.<br>
As illustrated in the figure below, we observe that all possible `2^(len(nums))` unique subsets are at the last level.

[![78.png](https://i.postimg.cc/Yqn02xs5/78.png)](https://postimg.cc/7fzwmSD9)

```python
def subsets(nums: List[int]) -> List[List[int]]:
    def _dfs(i: int) -> None:
        if i == len(nums):
            res.append(subset.copy())
            return

        # Left child - add the visited number
        subset.append(nums[i])
        _dfs(i + 1)

        # Right child - skip the visited number
        subset.pop()
        _dfs(i + 1)

    subset = []
    res = []
    _dfs(0)

    return res
```
* Time complexity: $O(n * 2^n)$
	* The recursive function is called $2^{n+1} - 1$ times.
	* Copying the subset with the average length of $\frac{n}{2}$ for each call takes $O(n)$.
* Space complexity: $O(n * 2^n)$
	* The result power set storing all possible subsets takes $O(n * 2^n)$.
	* The depth of call stack takes $O(n)$.
##### Comment
* Because we need to go down to the deepest level and **no pruning** is performed along any path, I don't think this is a backtracking solution by definition.
#### 2. Backtracking - DFS 
Instead of recording the last level only, we can use the **pruning** technique to avoid duplicated subset generations. Concretely speaking, we keep tracking the start index of `nums`, at which the visited element is added to the current subset.

[![78-2.png](https://i.postimg.cc/TYZNBqNJ/78-2.png)](https://postimg.cc/9rYB7ThD)

```python
def subsets(nums: List[int]) -> List[List[int]]:
    def _dfs(i: int, subset: List[int]) -> None:
        res.append(subset.copy())
        for j in range(i, len(nums)):
            subset.append(nums[j])
            _dfs(j+1, subset)
            subset.pop()

    res = []
    _dfs(0, [])

    return res
```
* Time complexity: $O(n * 2^n)$
	* The recursive function is called $2^{n}$ times, which is less than idea 1.
	* Copying the subset with the average length of $\frac{n}{2}$ for each call takes $O(n)$.
* Space complexity: $O(n * 2^n)$
	* The result power set storing all possible subsets takes $O(n * 2^n)$.
	* The depth of call stack takes $O(n)$.
#### 3. BFS
Another idea is just similar to level-order traversal. We can think of the temporary `res` as a queue, and add `num` to each subset in it (because we need all subsets, no explicit pop will be performed).
```python
def subsets(nums: List[int]) -> List[List[int]]:
    res = [[]]
    for num in nums:
        for i in range(len(res)):
            subset = res[i].copy()
            subset.append(num)
            res.append(subset)

    return res
```
*  Time complexity: $O(n * 2^n)$
	* The number of iterations is $2^n - 1$, hence taking $O(2^n)$.
	* Copying the subset with the average length of $\frac{n}{2}$ for each call takes $O(n)$.
* Space complexity: $O(n * 2^n)$
	* The result power set storing all possible subsets takes $O(n * 2^n)$.
#### 4. Bit Manipulation
#### References
* [Time complexity analysis](https://leetcode.com/problems/subsets/solutions/27281/a-general-approach-to-backtracking-questions-in-java-subsets-permutations-combination-sum-palindrome-partitioning/comments/333407)
* [Explanation on BFS solution](https://leetcode.com/problems/subsets/solutions/122645/3ms-easiest-solution-no-backtracking-no-bit-manipulation-no-dfs-no-bullshit/comments/283606)