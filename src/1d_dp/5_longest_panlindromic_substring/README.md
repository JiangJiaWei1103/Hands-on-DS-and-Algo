# [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
> Return the longest palindromic substring in a string.
* A **substring** is a contiguous **non-empty** sequence of characters within a string.
### Input
* `s`: A string.
	* `1 <= s.length <= 1000`
	* `s` consist of only digits and English letters.
### Output
The longest palindromic substring.
### Example
```
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```
### Idea
#### 1. Brute-force 
The most naive solution is to run palindrome check on any possible substring in the string.
```python
def longestPalindrome(s: str) -> str:
    if len(s) == 1:
        return s

    res = s[0]
    max_len = 1
    for i in range(len(s)):
        for j in range(i+1, len(s)):
            t = s[i:j+1]
            if t == t[::-1] and len(t) > max_len:
                res = t
                max_len = len(t) 

    return res
```
* Time complexity: $O(n^3)$
	* There exist $\frac{n(n-1)}{2}$ substrings, which takes $O(n^2)$ to traverse.
	* Reversing and linear scanning each substring to check if it's a palindrome takes $O(n)$.
* Space complexity: $O(n)$
	* A temporary substring under checking takes $O(n)$.
#### 2. Expansion from Center
Instead of linearly scanning from the start to the end of a substring, we can use the property of a palindrome, **mirroring**. To be concrete, we expand the substring from the visited center and keep checking if the character on the lhs is the same as the one on the rhs.

[![5.png](https://i.postimg.cc/WbXFgN9N/5.png)](https://postimg.cc/svGgRrdq)

```python
def longestPalindrome(s: str) -> str:
    def _longest_palindrome(l: int, r: int) -> str:
        """Return longest palindrome centered at (l+r) / 2."""
        res = ""
        while l >= 0 and r < len(s) and s[l] == s[r]:
            res = s[l:r+1]  # Slicing overhead
            l -= 1
            r += 1

        return res

    res = ""
    for c in range(len(s)):
        res_odd = _longest_palindrome(c, c)
        res_even = _longest_palindrome(c, c+1)

        if len(res_odd) > len(res):
            res = res_odd
        if len(res_even) > len(res):
            res = res_even

    return res


def longestPalindrome(s: str) -> str:
    def _longest_palindrome(l: int, r: int) -> str:
        """Return longest palindrome centered at (l+r) / 2."""
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l -= 1
            r += 1

        return res[l+1:r]  # Restore the previous status 

    res = ""
    for c in range(len(s)):
        res_odd = _longest_palindrome(c, c)
        res_even = _longest_palindrome(c, c+1)

        if len(res_odd) > len(res):
            res = res_odd
        if len(res_even) > len(res):
            res = res_even

    return res
```
* Time complexity: $O(n^2)$
	* There exist $2n - 1$ centers to traverse, taking $O(n)$.
	* Expanding from each center takes $O(n)$.
* Space complexity: $O(n)$
	* A temporary substring under checking takes $O(n)$.
##### Comment
* Space complexity can be further improved by two pointers to keep updating the start and the end positions of the substring.
```python
def longestPalindrome(s: str) -> str:
    def _longest_palindrome(l: int, r: int) -> Tuple[int, int]:
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l -= 1
            r += 1
            
        return l + 1, r

    start, end = 0, 0
    for c in range(len(s)):
        start_odd, end_odd = _longest_palindrome(c, c)
        start_even, end_even = _longest_palindrome(c, c+1)

        if end_odd - start_odd > start - end:
            start, end = start_odd, end_odd
        if end_even - start_even > start - end:
            start, end = start_even, end_even 

    return s[start:end]
```
#### 3. 2d DP with DP Table
In addition, this problem can be solved by the dp approach. We can break down the problem into sub-problems by the following recurrence relationship,
```math
f(i, j) = f(i + 1, j - 1) & (s[i] == s[j])
```
Concretely speaking, if a substring `s[i+1:j-1]` (both sides inclusive) is a palindrome, we can guarantee that `s[i:j]` is also a palindrome if the character at the position `i` is equal to that at the position `j`.

[![5-2.png](https://i.postimg.cc/HndsB9by/5-2.png)](https://postimg.cc/phcHd8GW)

```python
def longestPalindrome(s: str) -> str:
    s_len = len(s)
    dp = [[False] * s_len for _ in range(s_len)]

    for i in range(s_len):
        dp[i][i] = True

    start, max_len = 0, 1 
    for i in range(s_len-1, -1, -1):
        for j in range(i+1, s_len):
            if s[i] == s[j]:
                if j - i <= 2 or dp[i+1][j-1]:
                    dp[i][j] = True
                    if j - i + 1 > max_len:
                        max_len = j - i + 1
                        start = i
    
    return s[start:start+max_len]
```
* Time complexity: $O(n^2)$
	* Based on dp, we only need to check $\approx \frac{n^2}{2}$ `(i, j)` pairs, each of which takes constant time to completet.
* Space complexity: $O(n^2)$
	* A 2d dp table tracking the palindrome states takes $O(n^2)$.
#### *Manacher's Algorithm*
To be updated...