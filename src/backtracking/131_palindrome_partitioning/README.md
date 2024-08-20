# [131. Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/)
> Find all string partitions that contain only palindromes.
### Input
* `s`: A string.
### Output
All partitions that contain only palindromes.
### Example
```
Input: s = "aab"
Output: [["a","a","b"],["aa","b"]]
```
### Idea
To partition a string of length $L$, we have $(L-1)$ possible partition points. The most intuitive solution is to check all partitions using the brute-force approach. However, there might exist many unnecessary checks because some temporary partitions can't lead to a valid answer. Hence, we prune branches when the current substring to check isn't a palindrome (marked with a red cross below). 

[![131.png](https://i.postimg.cc/PrHm00jr/131.png)](https://postimg.cc/yWfgZQxw)

#### 1. Backtracking DFS
We keep partition the given string with the following core logic:
1. Start partition from the **start point**.
	* Initialize to 0 (check from the beginning).
2. Check if the candidate palindrome passes the test.
	* If `False`, prune the branch.
3. For any branch that's possible to lead to a valid answer,
	* Extend the current partition with the new palindrome that passes the test.
	* Continue partitioning with a new start point `j + 1` (i.e., the first char of the unseen substring).

Finally, if the string is completely partitioned, record the answer and return.
```python
def partition(s: str) -> List[List[str]]:
    def _is_palindrome(s: str) -> bool:
        l, r = 0, len(s) - 1
        while l < r:
            if s[l] != s[r]:
                return False

            l += 1
            r -= 1

        return True

    def _dfs(part: List[str], i: int) -> None:
        if i == len(s):
            res.append(part.copy())
            return

        for j in range(i, len(s)):
            cand = s[i:j+1]  # Candidate palindrome
            if _is_palindrome(cand):
                part.append(cand)
                _dfs(part, j + 1)
                part.pop()
    
    res = []
    _dfs([], 0)

    return res
```
* Time complexity: $O(L * 2^L)$, where $L$ is the string length.
	* For the string of length $L$, there are $(L-1)$ possible partition points, at which we can choose to partition or not. Hence, it takes $O(2^L)$.
	* For each candidate palindrome, we need to perform a palindrome check, which takes $O(L)$.
* Space complexity: $O(L)$
	* The depth of call stack takes $O(L)$.
		* Think about a single-char partition path.
