# [417. Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)
> Find all cells of a 2D grid in which the water can flow to both Pacific and Atlantic oceans. Note that the water can flow to neighbor cells with heights less than or equal to the current cell.
### Input
* `heights`: A 2D grid representing heights of cells.
	* `m == heights.length`
	* `n == heights[r].length`
	* `1 <= m, n <= 200`
	* `0 <= heights[r][c] <= 10^5`
### Output
A list containing all cells in which the water can flow to both Pacific and Atlantic oceans.
### Example
```
Input: heights = [[1,2,2,3,5],[3,2,3,4,4],[2,4,5,3,1],[6,7,1,4,5],[5,1,1,2,4]]
Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
Explanation: The following cells can flow to the Pacific and Atlantic oceans, as shown below:
[0,4]: [0,4] -> Pacific Ocean 
       [0,4] -> Atlantic Ocean
[1,3]: [1,3] -> [0,3] -> Pacific Ocean 
       [1,3] -> [1,4] -> Atlantic Ocean
[1,4]: [1,4] -> [1,3] -> [0,3] -> Pacific Ocean 
       [1,4] -> Atlantic Ocean
[2,2]: [2,2] -> [1,2] -> [0,2] -> Pacific Ocean 
       [2,2] -> [2,3] -> [2,4] -> Atlantic Ocean
[3,0]: [3,0] -> Pacific Ocean 
       [3,0] -> [4,0] -> Atlantic Ocean
[3,1]: [3,1] -> [3,0] -> Pacific Ocean 
       [3,1] -> [4,1] -> Atlantic Ocean
[4,0]: [4,0] -> Pacific Ocean 
       [4,0] -> Atlantic Ocean
Note that there are other possible paths for these cells to flow to the Pacific and Atlantic oceans.
```
### Idea
#### 1. Brute-force
The most intuitive solution is to run either DFS or BFS starting from each cell and check if there exist paths that can flow to both oceans. However, there can be many duplicated searches of paths in this method because we need to reset the visiting status for each cell in the grid.
```python
def pacificAtlantic(heights: List[List[int]]) -> List[List[int]]:
    OFFSETS: List[int] = [0, 1, 0, -1, 0]

    def _dfs(i: int, j: int) -> None:
        visited[i][j] = True
        if i == 0 or j == 0:
            flow[0] = True
        if i ==  m-1 or j == n-1:
            flow[1] = True

        for d in range(4):
            ni, nj = i + OFFSETS[d], j + OFFSETS[d+1]
            if (
                (0 <= ni < m)
                and (0 <= nj < n)
                and (not visited[ni][nj])
                and (heights[ni][nj] <= heights[i][j])
            ):
                _dfs(ni, nj)
    
    m, n = len(heights), len(heights[0])
    res = []
    for i in range(m):
        for j in range(n):
            flow = [False, False]
            visited = [[False] * n for _ in range(m)]
            _dfs(i, j)
            if flow[0] and flow[1]:
                res.append([i, j])
    
    return res
```
* Time complexity: $O((mn)^2)$
	* Running the searching algorithm (which takes $O(mn)$) for each of $mn$ cells takes $O((mn)^2)$.
* Space complexity: $O(mn)$
	* A 2D array recording the visiting status takes $O(mn)$.
	* The depth of call stack takes $O(mn)$.
#### 2. Seawater Intrusion - DFS
Instead of considering water flowing from cells to the ocean, we can think of flowing as a reversed intrusion process, in which the seawater flows backward from the ocean into cells. To be concrete, we run the searching algorithm, either DFS or BFS, starting from cells near the border. Key points are summarized as follows,
1. Run DFS from the top and the left borders for the Pacific ocean.
2. Run DFS from the right and the bottom borders for the Atlantic ocean.
3. Avoid duplicated visitings on the same cell.

Finally, return the cells in which the water can flow to both oceans.
```python
def pacificAtlantic(heights: List[Tuple[int]]) -> List[List[int]]:
    OFFSETS: List[int] = [0, 1, 0, -1, 0]

    def _dfs(i: int, j: int, ocean: Set[List[int]]) -> None:
        ocean.add((i, j))

        for d in range(4):
            ni, nj = i + OFFSETS[d], j + OFFSETS[d+1]
            if (
                (0 <= ni < m)
                and (0 <= nj < n)
                and (not (ni, nj) in ocean)
                and (heights[ni][nj] >= heights[i][j])
            ):
                _dfs(ni, nj, ocean)

    m, n = len(heights), len(heights[0])
    pac, atl = set(), set()
    for i in range(m):
        _dfs(i, 0, pac)
        _dfs(i, n-1, atl)
    for j in range(n):
        _dfs(0, j, pac)
        _dfs(m-1, j, atl)
    res = list(pac.intersection(atl))

    return res
```
* Time complexity: $O(mn)$
	* Visiting each cell at most once because we avoid any duplicated visiting, hence $O(mn)$.
* Space complexity: $O(mn)$
	* Two sets recording the visited cells take $O(mn)$.
	* The depth of call stack takes $O(mn)$.
#### 3. Seawater Intrusion - BFS
The core concept is the same as idea 2. For implementation, we use two queues, one for the Pacific and the other for the Atlantic. Also, we use two 2D matrices to track the intrusion status of cells and avoid duplicated visitings at the same time.
```python
def pacificAtlantic(heights: List[List[int]]) -> List[List[int]]:
    OFFSETS: List[int] = [0, 1, 0, -1, 0]

    def _bfs(q: deque, visited: List[List[int]]) -> None:
        while len(q) != 0:
            i, j = q.popleft()
            visited[i][j] = True
            for d in range(4):
                ni, nj = i + OFFSETS[d], j + OFFSETS[d+1]
                if (
                    (0 <= ni < m)
                    and (0 <= nj < n)
                    and (not visited[ni][nj])
                    and (heights[ni][nj] >= heights[i][j])
                ):
                    q.append((ni, nj))

    m, n = len(heights), len(heights[0])
    pq = deque([(0, j) for j in range(n)] + [(i, 0) for i in range(1, m)])
    aq = deque([(m-1, j) for j in range(n)] + [(i, n-1) for i in range(0, m-1)])
    pac = [[False] * n for _ in range(m)]
    atl = [[False] * n for _ in range(m)]
    _bfs(pq, pac)
    _bfs(aq, atl)
    
    res = []
    for i in range(m):
        for j in range(n):
            if pac[i][j] and atl[i][j]:
                res.append([i, j])

    return res
```
* Time complexity: $O(mn)$
	* Visiting each cell at most once because we avoid any duplicated visiting, hence $O(mn)$.
* Space complexity: $O(mn)$
	* Two matrices recording the intrusion status take $O(mn)$.
	* <span style="color: orange;">(?)</span> Two queues recording the next-to-visit cells take $O(mn)$.