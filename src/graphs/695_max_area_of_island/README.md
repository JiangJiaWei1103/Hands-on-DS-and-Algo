# [695. Max Area of Island](https://leetcode.com/problems/max-area-of-island/)
> Find the maximum area of an island in `grid`. If there's no island, return `0`.
### Input
* `grid`: An `m x n` 2D array representing a map containing waters `0`'s and lands  `1`'s.
	* `m == grid.length`
	* `n == grid[i].length`
	* `1 <= m, n <= 50`
	* `grid[i][j]` is either `0` or `1`.
### Output
The maximum are of an island if any else `0`.
### Example
```
Input: grid = [
	[0,0,1,0,0,0,0,1,0,0,0,0,0],
	[0,0,0,0,0,0,0,1,1,1,0,0,0],
	[0,1,1,0,1,0,0,0,0,0,0,0,0],
	[0,1,0,0,1,1,0,0,1,0,1,0,0],
	[0,1,0,0,1,1,0,0,1,1,1,0,0],
	[0,0,0,0,0,0,0,0,0,0,1,0,0],
	[0,0,0,0,0,0,0,1,1,1,0,0,0],
	[0,0,0,0,0,0,0,1,1,0,0,0,0]
]
Output: 6
Explanation: The answer is not 11, because the island must be connected 4-directionally.
```
### Idea
The core concept is nearly the same as [[Hack-the-Algo-and-DS/Grind-LeetCode/src/graphs/200_number_of_islands/README|200. Number of Islands]]. The only difference is that we need to calculate the area of each island, instead of the number of islands. 
#### 1. DFS
The basic idea is to go as far as we can. To calculate the area, we add up areas of the central cell and those from the four directions.
```python
def maxAreaOfIsland(grid: List[List[int]]) -> int:
    def _dfs(i: int, j: int) -> int:
        """Calculate the area of the island starting from the cell ij."""
        if ((i < 0) or (i == m)
            or (j < 0) or (j == n)
            or visited[i][j]
            or grid[i][j] == 0):
            return 0

        visited[i][j] = True
        area_l = _dfs(i, j-1)
        area_u = _dfs(i-1, j)
        area_r = _dfs(i, j+1)
        area_d = _dfs(i+1, j)

        # Add up areas of the central cell and those from four dirs
        area = 1 + area_l + area_u + area_r + area_d

        return area

    m, n = len(grid), len(grid[0])
    max_area = 0
    visited = [[False] * n for _ in range(m)]
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1 and not visited[i][j]:
                area = _dfs(i, j)
                max_area = max(area, max_area)

    return max_area
```
* Time complexity: $O(mn+4mn) \approx O(mn)$
* Space complexity: $O(mn)$
#### 2. BFS
The basic idea is to go as broad as we can.
```python
def maxAreaOfIsland(grid: List[List[int]]) -> int:
    def _bfs(i: int, j: int) -> int:
        """Calculate the area of the island starting from the cell ij."""
        area = 0
        q = deque([(i, j)])
        while len(q) > 0:
            i, j = q.popleft()
            if ((0 <= i < m)
                and (0 <= j < n)
                and (not visited[i][j])
                and (grid[i][j] == 1)):
                visited[i][j] = True 
                area += 1
                for r, c in zip (
                    (i, i-1, i, i+1),
                    (j-1, j, j+1, j)
                ): 
                    q.append((r, c))

        return area

    m, n = len(grid), len(grid[0])
    max_area = 0
    visited = [[False] * n for _ in range(m)]
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1 and not visited[i][j]:
                area = _bfs(i, j)
                max_area = max(area, max_area)

    return max_area
```
* Time complexity: $O(mn+4mn) \approx O(mn)$
* Space complexity: $O(mn)$
	* <span style="color: orange;">?</span> A queue storing the potential lands to explore takes $O(m+n)$ in the worst case.
## Discussion
* What's the space complexity of a queue in bfs solution? Read [this](https://stackoverflow.com/questions/50901203/dfs-and-bfs-time-and-space-complexities-of-number-of-islands-on-leetcode).