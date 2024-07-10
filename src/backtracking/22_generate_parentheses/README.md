# [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
> Generate all well-formed (valid) parentheses combinations given `n` pairs of parentheses
### Input
* `n`: Number of parentheses pairs.
	* `1 <= n <= 8`
### Output
All valid combinations of `n` parentheses.
### Example
```
Input: n = 3
Output: ["((()))", "(()())", "(())()", "()(())", "()()()"]
```
### Idea
The most intuitive solution is to generate all possible combinations then check if the combination is valid or not. This brute-force solution will take $O(2^{2n} * n)$  because we need to linearly scan $2^{2n}$ combinations, each of which should take $O(2n)$ to complete.<br>
To avoid enumerating all possible combinations, the following constraints can be taken into consideration when generating candidate combinations,
1. A valid combination should contain `n` open and `n` close parentheses.
2. For a valid candidate combination, the number of open parentheses should be always greater than or equal to that of close ones.
#### 1. Backtracking with DFS
Considering constraints mentioned above, we use the concept of backtracking to generate all **valid** combinations. For each current combination, we have two options, the first is to add a `"("` and the second is to add a `")"`. However, we need to make sure that the parenthesis is added under constraints.<br>
One thing to note is that the **undo** operation in backtracking is **implicitly** done in the following implementation.
```python
def generateParenthesis(n: int) -> List[str]:
    def _dfs(comb: str, n_opens: int, n_closes: int) -> None:
        if n_opens > n or n_opens < n_closes:
            return
        if n_opens == n and n_closes == n:
            res.append(comb)
            return

        _dfs(comb + "(",  n_opens + 1, n_closes)
        _dfs(comb + ")", n_opens, n_closes + 1)

    res = []
    _dfs("(", 1, 0)

    return res


def generateParenthesis(n: int) -> List[str]:
    def _dfs(comb: str, n_opens: int, n_closes: int) -> None:
        if n_opens == n_closes == n:
            res.append(comb)
            return

        if n_opens < n:
            _dfs(comb + "(", n_opens + 1, n_closes)
        if n_closes < n_opens:
            _dfs(comb + ")", n_opens, n_closes + 1)

    res = []
    _dfs("", 0, 0)

    return res
```
* Time complexity: $O(2^{2n})$
	* For a combination with $2n$ characters, we need to consider at most $2^{2n}$ possibilities.
	* See [here](https://leetcode.com/problems/generate-parentheses/solutions/10100/easy-to-understand-java-backtracking-solution/comments/184522).
* Space complexity: $O(n)$
	* The depth of call stack takes $O(2n) \approx O(n)$, where $2n$ is the number of total characters in a valid combination.
#### 2. Backtracking with BFS
The core concept is the same as idea 1.
```python
def generateParenthesis(n: int) -> List[str]:
    res = []
    q = deque([("(", 1, 0)])
    while len(q) != 0:
        comb, n_opens, n_closes = q.pop()
        if n_opens == n_closes == n:
            res.append(comb)
            continue

        if n_opens < n:
            q.append((comb + "(", n_opens + 1, n_closes))
        if n_closes < n_opens:
            q.append((comb + ")", n_opens, n_closes + 1))

    return res
```
* Time complexity: $O(2^{2n})$
	* For a combination with $2n$ characters, we need to consider at most $2^{2n}$ possibilities.
* Space complexity: $O(2^{2n})$
	*  Consider the last level?
#### 3. DP
## References
* [括号生成](https://leetcode.cn/problems/generate-parentheses/solutions/192912/gua-hao-sheng-cheng-by-leetcode-solution/)
* [Catalan number | WIKIPEDIA](https://en.wikipedia.org/wiki/Catalan_number)
