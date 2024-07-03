# [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)
> Return the number of islands (*e.g.,* connected lands surrounded by water) in a 2D map.
### Input
* `grid`: An `m x n` 2D grid.
	* `m == grid.length`
	* `n == grid[i].length`
	* `1 <= m, n <= 300`
	* `grid[i][j]` is `'0'` or `'1'`.
### Output
Number of islands.
### Example
```
Input: grid = [
  ["1", "1", "1", "1", "0"],
  ["1", "1", "0", "1", "0"],
  ["1", "1", "0", "0", "0"],
  ["0", "0", "0", "0", "0"]
]
Output: 1
```
### Idea
#### 1. DFS
We can think of the process as expanding the range of the island from a specific starting point. The key idea is to go as far as we can if there's any potential land for us to expand. Hence, we can write down the following base cases in which we'll stop the expansion,
1. The expansion is out of range of the grid (*i.e.,* exceeding the edges).
2. The current entry has been visited.
3. The current entry is water, not land.

As long as we know that the current entry is an **unvisited land**, we mark it as visited first and keep expanding the island towards four directions. Again, we want to go as far (*i.e.,* as deep) as we can.
```python
def numIslands(grid: List[List[str]]) -> int:
    def _dfs(i: int, j: int) -> None:
        """Expand island based on DFS."""
        if ((i < 0 or i == m) 
            or (j < 0 or j == n)
            or visited[i][j]
            or grid[i][j] == "0"):
            return

        visited[i][j] = True
        _dfs(i + 1, j)
        _dfs(i, j + 1)
        _dfs(i - 1, j)
        _dfs(i, j - 1)

    m, n = len(grid), len(grid[0])
    visited = [[False] * n for _ in range(m)]
    n_islands = 0
    for i in range(m):
        for j in range(n):
            if not visited[i][j] and grid[i][j] == "1":
                _dfs(i, j)
                n_islands += 1

    return n_islands
```
* Time complexity: $O(mn + 4*mn) \approx O(mn)$
	* Each entry can be visited at most 5 times (*i.e.,* in the nested loop and from other entries' search).
*  Space complexity: $O(mn)$
	* A 2D matrix tracking the visiting status takes $O(mn)$.
	* <span style="color: orange;">?</span> The depth of call stack takes $O(mn)$ in the worst case (*i.e.,* think about all lands are connected).
##### Comment
* We can improve the space complexity if we can just override `grid` to track the visiting status simultaneously.
```python
def numIslands(grid: List[List[str]]) -> int:
    def _dfs(i: int, j: int) -> None:
        if ((i < 0 or i == m) 
            or (j < 0 or j == n)
            or grid[i][j] != "1"):
            return

        grid[i][j] = "-1"
        _dfs(i + 1, j)
        _dfs(i, j + 1)
        _dfs(i - 1, j)
        _dfs(i, j - 1)

    m, n = len(grid), len(grid[0])
    n_islands = 0
    for i in range(m):
        for j in range(n):
            if grid[i][j] == "1":
                _dfs(i, j)
                n_islands += 1

    return n_islands
```

#### 2. BFS
The difference of BFS with DFS is the direction of expansion. In BFS, we try to go as broad as we can first.
```python
def numIslands(grid: List[List[str]]) -> int:
    def _bfs(i: int, j: int) -> None:
        q = deque([(i, j)])
        while len(q) > 0:
            i, j = q.popleft()
            if ((0 <= i < m) and (0 <= j < n)
                and (grid[i][j] == "1")):
                grid[i][j] = "-1"  # Sink the land
                for r, c in zip(
                    (i+1, i, i-1, i),
                    (j, j+1, j, j-1)
                ):
                    q.append((r, c))

    m, n = len(grid), len(grid[0])
    n_islands = 0
    for i in range(m):
        for j in range(n):
            if grid[i][j] == "1":
                _bfs(i, j)
                n_islands += 1

    return n_islands
```
* Time complexity: $O(mn)$
	* See the explanation of DFS.
*  Space complexity: $O(m+n)$, <span style="color: orange;">the outermost range?</span>
	* <span style="color: orange;">?</span> A queue storing the potential lands takes $O(m+n)$ in the worst case (*i.e.,* think about all lands are connected).
