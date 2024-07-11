# [130. Surrounded Regions](https://leetcode.com/problems/surrounded-regions/)
> Capture all regions represented by connected `"O"`'s that are surrounded by `"X"`'s and don't touch any edge.
### Input
* `board`: An `m x n` matrix.
	* `m == board.length`
	* `n == board[i].length`
	* `1 <= m, n <= 200`
	* `board[i][j]` is `'X'` or `'O'`.
### Output
In-place capture all valid regions. That is, turn valid regions from `"O"` to `"X"`.
### Example
```
Input: board = [
	["X","X","X","X"],
	["X","O","O","X"],
	["X","X","O","X"],
	["X","O","X","X"]
]
Output: [
	["X","X","X","X"],
	["X","X","X","X"],
	["X","X","X","X"],
	["X","O","X","X"]
]
```
### Idea
To capture regions that don't touch any edge, we can think about marking (*i.e.,* setting a temporary state) those touching at least one edge. Then, we are able to safely capture the regions that haven't been marked. The process can be summarized as follows,
1. Start running searching algorithm from `"O"` on any edge.
2. Set a temporary state for each visited `"O"`.
3. Safely capture the remaining regions and recover marked regions back to `"O"`.

For searching, we can choose either DFS or BFS.
#### 1. DFS
```python
def solve(board: List[List[str]]) -> None:
    OFFSETS: List[str] = [0, 1, 0, -1, 0]

    def _dfs(i: int, j: int) -> None:
        board[i][j] = "-"
        for d in range(4):
            ni, nj = i + OFFSETS[d], j + OFFSETS[d+1]
            if (
                (0 <= ni < m)
                and (0 <= nj < n)
                and (board[ni][nj] == "O")
            ):
                _dfs(ni, nj)
    
    m, n = len(board), len(board[0])
    edges = (
        [(0, j) for j in range(n)]
        + [(m-1, j) for j in range(n)]
        + [(i, 0) for i in range(1, m-1)]
        + [(i, n-1) for i in range(1, m-1)]
    )
    for i, j in edges:
        if board[i][j] == "O":
            _dfs(i, j)

    for i in range(m):
        for j in range(n):
            if board[i][j] == "-":
                # Recover the marked regions
                board[i][j] = "O"
            elif board[i][j] == "O":
                # Capture the regions
                board[i][j] = "X"
```
* Time complexity: $O(mn)$
	* Running DFS on the board without visiting those having been visited takes $O(mn)$.
	* Final state recovery takes $O(mn)$.
* Space complexity: $O(mn)$
	* The depth of call stack takes $O(mn)$.
#### 2. BFS
```python
def solve(board: List[List[str]]) -> None:
    OFFSETS: List[str] = [0, 1, 0, -1, 0]
    
    m, n = len(board), len(board[0])
    q = deque()
    for i in range(m):
        for j in range(n):
            if (
                (i in {0, m-1} or j in {0, n-1})
                and (board[i][j] == "O")
            ):
                q.append((i, j))
    while len(q) != 0:
        i, j = q.popleft()
        if board[i][j] == "O":
            board[i][j] = "-"
            for d in range(4):
                ni, nj = i + OFFSETS[d], j + OFFSETS[d+1]
                if (
                    (0 <= ni < m)
                    and (0 <= nj < n)
                    and (board[ni][nj] == "O")
                ):
                    q.append((ni, nj))

    for i in range(m):
        for j in range(n):
            if board[i][j] == "-":
                board[i][j] = "O"
            elif board[i][j] == "O":
                board[i][j] = "X"
```
* Time complexity: $O(mn)$
	* Running BFS on the board without visiting those having been visited takes $O(mn)$.
	* Final state recovery takes $O(mn)$.
* Space complexity: <span style="color: orange">?</span> $O(mn)$ 
## Discussion
* Potential stack overflow. See [here](https://leetcode.com/problems/surrounded-regions/solutions/41612/a-really-simple-and-readable-c-solution-uff0conly-cost-12ms/comments/39811).