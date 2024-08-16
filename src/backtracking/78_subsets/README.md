# [78. Subsets](https://leetcode.com/problems/subsets/)
> Return the **power set** of an integer array. No duplicated subsets are allowed and the solution can be returned in any order.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 10`
	* `-10 <= nums[i] <= 10`
	* All the numbers of `nums` are **unique**.
### Output
The power set of an integer array.
### Example
```
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```
### Idea
#### 1. Full DFS
The idea of generating the power set is to decide whether to add the visited number or not. That is, we can choose to add `nums[i]` or keep it as is.

As illustrated in the figure below, we observe that all possible `2^(len(nums))` unique subsets are at the last level.

[![78.png](https://i.postimg.cc/Yqn02xs5/78.png)](https://postimg.cc/7fzwmSD9)

The reason why we call it a full DFS is that we observe that backtracking never happens with a early pruning. Following provides two implementations, both of which need to pop the added element regardless of the order. Instead of interpreting the pop operation as backtracking, it's just used to restore the original state before returning to the previous level in DFS. See @Sportsandgames6's explanation [here](https://www.youtube.com/watch?v=REOH22Xwdkk).
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


def subsets(nums: List[int]) -> List[List[int]]:
    def _dfs(subset: List[int], i: int) -> None:
        if i == len(nums):
            res.append(subset.copy())
            return

        _dfs(subset, i + 1)
        
        subset.append(nums[i])
        _dfs(subset, i + 1)
        subset.pop()

    res = []
    _dfs([], 0)

    return res
```
* Time complexity: $O(n * 2^n)$
	* The recursive function is called $2^{n+1} - 1$ times.
	* Copying the subset with the average length of $\frac{n}{2}$ for each call takes $O(n)$.
* Space complexity: $O(n * 2^n)$
	* The result power set storing all possible subsets takes $O(n * 2^n)$.
	* The depth of call stack takes $O(n)$.
#### 2. Backtracking DFS 
Instead of recording the last level only, we can use the **pruning** technique to avoid duplicated subset generations. Concretely speaking, we keep track of the start index of `nums` to add at the next level, which restricts the valid numbers for different branches (e.g., there exist no valid number to add for the rightmost branch in the following figure).

[![78-2.png](https://i.postimg.cc/TYZNBqNJ/78-2.png)](https://postimg.cc/9rYB7ThD)

```python
def subsets(nums: List[int]) -> List[List[int]]:
    def _dfs(subset: List[int], i: int) -> None:
        res.append(subset.copy())

        for j in range(i, len(nums)):
            subset.append(nums[j])
            _dfs(subset, j + 1)
            subset.pop()

    res = []
    _dfs([], 0)

    return res
```
* Time complexity: $O(n * 2^n)$
	* The recursive function is called $2^{n}$ times, which is less than idea 1.
	* Copying the subset with the average length of $\frac{n}{2}$ for each call takes $O(n)$.
* Space complexity: $O(n * 2^n)$
	* The result power set storing all possible subsets takes $O(n * 2^n)$.
	* The depth of call stack takes $O(n)$.
#### 3. BFS
Another idea is just similar to level-order traversal. We can think of the temporary `res` as a queue, and add `num` to each subset in it. Because we need all subsets, no explicit pop will be performed. Or, we can just interpret it from a view of dynamic programming.
```
nums = [1, 2, 3]
========================
queue []
-- Add 1 --
queue [] | [1]
-- Add 2 --
queue [] [1] | [2] [1, 2]
-- Add 3 --
queue [] [1] [2] [1, 2] | [3] [1, 3] [2, 3] [1, 2, 3]
```

```python
def subsets(nums: List[int]) -> List[List[int]]:
    res = [[]]
    for num in nums:
        for i in range(len(res)):
            res.append(res[i] + [num])

    return res
```
*  Time complexity: $O(n * 2^n)$
	* The number of iterations is $2^n - 1$ (i.e., the number of subsets left to `|` in illustration above), hence taking $O(2^n)$.
	* Copying the subset with the average length of $\frac{n}{2}$ for each call takes $O(n)$.
* Space complexity: $O(n * 2^n)$
	* The result power set storing all possible subsets takes $O(n * 2^n)$.
#### 4. Bit Manipulation
#### References
* [Time complexity analysis](https://leetcode.com/problems/subsets/solutions/27281/a-general-approach-to-backtracking-questions-in-java-subsets-permutations-combination-sum-palindrome-partitioning/comments/333407)
* [Explanation on BFS solution](https://leetcode.com/problems/subsets/solutions/122645/3ms-easiest-solution-no-backtracking-no-bit-manipulation-no-dfs-no-bullshit/comments/283606)