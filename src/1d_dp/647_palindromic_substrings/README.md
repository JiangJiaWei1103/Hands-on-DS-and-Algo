# [647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
> Return the number of palindromic substrings in a string.
* A string is a **palindrome** when it reads the same backward as forward.
* A **substring** is a contiguous sequence of characters within the string.
### Input
* `s`: A string.
	* `1 <= s.length <= 1000`
	* `s` consists of lowercase English letters.
### Output
The number of palindromic substrings in the string.
### Example
```
Input: s = "abc"
Output: 3
Explanation: Three palindromic strings: "a", "b", "c".
```
### Idea
The core idea is the same as [[Hack-the-Algo-and-DS/Grind-LeetCode/src/1d_dp/5_longest_panlindromic_substring/README|problem 5]].
#### 1. Brute-force
The most intuitive solution is to check all possible substrings in the string.
```python
def countSubstrings(s: str) -> int:
    s_len = len(s)
    cnt = 0
    for i in range(s_len):
        for j in range(i+1, s_len+1):
            t = s[i:j]
            if t == t[::-1]:
                cnt += 1

    return cnt
```
* Time complexity: $O(n^3)$
	* There exist $\frac{n(n-1)}{2}$ possible substrings to traverse, which takes $O(n^2)$ to complete.
	* Slicing a substring, reversing it, and linearly scanning for comparion take $O(n)$.
* Space complexity: $O(n)$
	* Slicing a list has a copy operation which takes $O(n)$.
#### 2. Expansion from Center
```python
def countSubstrings(s: str) -> int:
    def _count_substrings(l: int, r: int) -> int:
        """Count all palindromic substrings for center (l+r) / 2."""
        cnt = 0
        while l >= 0 and r < len(s) and s[l] == s[r]:
            cnt += 1
            l -= 1
            r += 1

        return cnt

    cnt = 0 
    for i in range(len(s)):
        cnt += _count_substrings(i, i)  # Odd
        cnt += _count_substrings(i, i+1)  # Even

    return cnt
```
* Time complexity: $O(n^2)$
	* There are $2n - 1$ centers to traverse, which takes $O(n)$ in total.
	* Expansion from each center takes $O(n)$.
* Space complexity: $O(1)$
#### 3. 2d DP with DP Table
```python
def countSubstrings(s: str) -> int:        
    s_len = len(s)
    dp = [[False] * s_len for _ in range(s_len)]
    cnt = s_len  # Diagonal

    for i in range(s_len):
        dp[i][i] = True

    for i in range(s_len-1, -1, -1):
        for j in range(i+1, s_len):
            if s[i] == s[j]:
                if j - i <= 2 or dp[i+1][j-1]:
                    dp[i][j] = True
                    cnt += 1

    return cnt
```
* Time complexity: $O(n^2)$
	* There exist $\frac{n(n-1)}{2}$ possible substrings to traverse, which takes $O(n^2)$ to complete.
* Space complexity: $O(n^2)$\
	* A 2d dp table tracking palindromic states takes $O(n^2)$.
#### 4. Manacher's Algorithm
To be updated...