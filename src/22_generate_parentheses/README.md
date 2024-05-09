# [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
> Generate all well-formed (valid) parentheses combinations given `n` pairs of parentheses

* [ ] Analyze time and space complexity.
* [ ] Solve the problem with DP.
* [ ] Complete discussions.
### Input
* `n`: Number of parentheses pairs.
### Output
All valid combinations of `n` parentheses.
### Example
```
Input: n = 3
Output: ["((()))", "(()())", "(())()", "()(())", "()()()"]
```
### Idea
To avoid enumerating all possible combinations using a **brute-force** approach, the following constraints can be taken into consideration when generating candidate combinations,
1. A valid combination should contain `n` open and `n` close parentheses.
2. For a valid candidate combination, the number of open parentheses should be always greater than or equal to close.
#### 1. Backtracking with DFS
For each call with current `param_comb`, try to add an open or a close parenthesis.
```python
def generateParenthesis(n: int) -> List[str]:
    def _dfs(paren_comb: str, n_opens: int, n_closes: int) -> None:
        """DFS parentheses combinations.
        
        Args:
            param_comb: Current parenthesis combination.
            n_opens: Number of remaining open parentheses.
            n_opens: Number of remaining close parentheses.
        """
        if n_opens > n_closes or n_opens < 0:
            return None

        if n_opens == 0 and n_closes == 0:
            # All parentheses are used in the current combination
            paren_combs.append(paren_comb)
            return None

        # Try to add an open parenthesis
        _dfs(paren_comb + "(", n_opens-1, n_closes)
        # Try to add a close parenthesis
        _dfs(paren_comb + ")", n_opens, n_closes-1)

    paren_combs = []
    _dfs("", n_opens=n, n_closes=n)

    return paren_combs


def generateParenthesis(self, n: int) -> List[str]:
    def _dfs(paren_comb: str, n_opens: int, n_closes: int) -> None:
        """DFS parentheses combinations.
        
        Args:
            param_comb: Current parenthesis combination.
            n_opens: Number of remaining open parentheses.
            n_opens: Number of remaining close parentheses.
        """
        if n_opens == 0 and n_closes == 0:
            # All parentheses are used in the current combination
            paren_combs.append(paren_comb)
            return None

        if n_opens > 0:
            # Try to add an open parenthesis
            _dfs(paren_comb + "(", n_opens-1, n_closes)
        if n_closes > n_opens:
            # Try to add a close parenthesis
            _dfs(paren_comb + ")", n_opens, n_closes-1)

    paren_combs = []
    _dfs("", n_opens=n, n_closes=n)

    return paren_combs
```
* Time complexity: $O()$
* Space complexity: $O()$
#### 2. Backtracking with BFS
Use a queue to store candidate combinations level by level.
```python
def generateParenthesis(n: int) -> List[str]:
    paren_combs = []
    q = deque([("", 0, 0)])
    while len(q) > 0:
        paren_comb, n_opens, n_closes = q.popleft()
        if n_opens + n_closes == n*2:
            paren_combs.append(paren_comb)
            continue

        if n_opens < n:
            q.append((paren_comb + "(", n_opens + 1, n_closes))

        if n_opens > n_closes:
            q.append((paren_comb + ")", n_opens, n_closes + 1))

    return paren_combs
```
* Time complexity: $O()$
* Space complexity: $O()$
#### 3. DP

### Discussion
#### Explain Difference btw DFS and BFS for Backtracking
#### The Relationship with Catalan Number