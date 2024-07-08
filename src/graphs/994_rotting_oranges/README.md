# [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)
> Calculate the minimum number of minutes that must elapse for all fresh oranges to rot. Note that the rotting process can only take place through 4 directions.
### Input
* `grid`: An `m x n` array with `0` representing the empty cell, `1` for a fresh orange, and `2` for a rotten one.
	* `m == grid.length`
	* `n == grid[i].length`
	* `1 <= m, n <= 10`
### Output
The minimum time for all oranges to rot if possible, else `-1`.
### Example
```
Input: grid = [
	[2, 1, 1],
	[0, 1, 1],
	[1, 0, 1]
]
Output: -1
Explanation: The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.
```
### Idea
#### 1. Multi-Source BFS
It's intuitive to come up with a BFS solution that describes the rotting as a diffusion process **level by level**. What's important is that the rotting process can start from multiple sources simultaneously in the beginning. The multi-source BFS can be summarized as follows,
1. Use a queue to track all rotting cells initially.
2. Run BFS to diffuse rotting **level by level**, because all fresh oranges on the same levels rot at the same time.
3. Keep running 2 until the diffusion is done or no fresh oranges are left.

For implementation, we initialize a offset list `OFFSETS` to help track neighbors on four directions. Also, there are two kinds of conditions to use to stop the BFS process,
1. Stop when the diffusion is done or **no fresh oranges are left**.
2. Stop when the diffusion is done.

The only difference is that the second condition will lead to one more round of counting, which can cause the problem similar to off-by-one error. Hence, we initialize `tot_t = -1` to handle this issue.
```python
def orangesRotting(grid: List[List[int]]) -> int:
    OFFSETS: List[int] = [0, 1, 0, -1, 0]

    m, n = len(grid), len(grid[0])
    tot_t = 0
    n_fresh = 0
    q = deque()
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1:
                n_fresh += 1
            elif grid[i][j] == 2:
                q.append((i, j))

    while len(q) != 0 and n_fresh > 0:
        for _ in range(len(q)):
            i, j = q.popleft()
            for d in range(4):
                ni, nj = i + OFFSETS[d], j + OFFSETS[d+1]
                if (
                    (0 <= ni < m)
                    and (0 <= nj < n)
                    and grid[ni][nj] == 1
                ):
                    grid[ni][nj] = 2
                    q.append((ni, nj))
                    n_fresh -= 1
        tot_t += 1

    return tot_t if n_fresh == 0 else -1


def orangesRotting(grid: List[List[int]]) -> int:
        OFFSETS: List[int] = [0, 1, 0, -1, 0]

        m, n = len(grid), len(grid[0])
        tot_t = -1
        n_fresh = 0
        q = deque()
        for i in range(m):
            for j in range(n):
                if grid[i][j] == 1:
                    n_fresh += 1
                elif grid[i][j] == 2:
                    q.append((i, j))

        while len(q) != 0:
            for _ in range(len(q)):
                i, j = q.popleft()
                for d in range(4):
                    ni, nj = i + OFFSETS[d], j + OFFSETS[d+1]
                    if (
                        (0 <= ni < m)
                        and (0 <= nj < n)
                        and grid[ni][nj] == 1
                    ):
                        grid[ni][nj] = 2
                        q.append((ni, nj))
                        n_fresh -= 1
            tot_t += 1

        if n_fresh > 0:
            return -1
        else:
            return 0 if tot_t == -1 else tot_t
```
* Time complexity: $O(mn + mn + 4mn) \approx O(mn)$
	* The first nested loop used to record cell status takes $O(mn)$.
	* For BFS, each cell can be visited or checked for at most 5 times (*i.e.,* as a center or one of the four directions). Hence, it can take $O(5mn)$.
* Space complexity: $O(mn)$
	* A queue storing rot oranges for rotting process takes $O(mn)$ in the worst case.