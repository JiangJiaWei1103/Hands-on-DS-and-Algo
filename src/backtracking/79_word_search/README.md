# [79. Word Search](https://leetcode.com/problems/word-search/)
> Check if a given word exists in a 2D grid.
### Input
* `board`: An `m x n` grid of characters.
	* `m == board.length`
	* `n = board[i].length`
	* `1 <= m, n <= 6`
* `word`: A string to search.
	* `1 <= word.length <= 15`
	* `board` and `word` consists of only lowercase and uppercase English letters.
### Output
True if the word is found, else False.
### Example
```
Input: board = [
	["A","B","C","E"],
	["S","F","C","S"],
	["A","D","E","E"]
], word = "SEE"
Output: true
```
### Idea
To check whether the word exists in the board or not, we follow the idea summarized as below:
1. Traverse the board with limitations.
	* We can't exceed the boundaries.
	* We can't use a character twice during a single round of searching.
2. For each visited cell, we continue searching towards at most 3 directions if the current cell is valid.
#### 1. Backtracking DFS
Overall, the core logic of `_dfs` is to check if the sub-word `word[ci:]` is found. If it's true for any of the 3 directions (note that we can't go to the cell where we just came from), we can return `True` to indicate the word is successfully found. What's important is that we need to define what a valid cell is:
1. `(i, j)` is within the board.
2. `(i, j)` hasn't been visited.

To stop the searching process, we return `False` if the current character check fails and return `True` if the last character is matched.
```python
def exist(board: List[List[str]], word: str) -> bool:
    DIRS = [0, 1, 0, -1, 0]

    def _dfs(i: int, j: int, ci: int) -> bool:
        """Search sub-word word[ci:]."""
        if board[i][j] != word[ci]:
            return False
        elif ci == len(word) - 1:
            # The entire word is found
            return True

        board[i][j] = "#"
        for d in range(4):
            i_, j_ = i + DIRS[d], j + DIRS[d+1]
            if (
                0 <= i_ < m
                and (0 <= j_ < n)
                and board[i_][j_] != "#"
                and _dfs(i_, j_, ci+1)
            ):
                return True

        # Word search fails for all four dirs of (i, j)
        # Backtrack, restoring the original visiting status
        board[i][j] = word[ci]

        return False

    m, n = len(board), len(board[0])
    for i in range(m):
        for j in range(n):
            if board[i][j] == word[0] and _dfs(i, j, 0):
                return True
    
    return False
```
* Time complexity: $O(mn * L^3)$, where $L$ is the word length.
	* A single searching process can be represented as a **ternary** tree, which takes $O(L^3)$ to complete.
	* The searching can start from any cell in the board, hence taking $O(mn * L^3)$ in total.
* Space complexity: $O(L)$
	* The depth of call stack takes $O(L)$, and the worst case happens when the word occupies the entire board, taking $O(mn)$.
##### Comment
* Without modifying `board` in-place to mark visited cells, we can either use another 2D array or a set to record the visited path. Note that **backtracking** is necessary to restore the original visiting status for the next possible searching process.
