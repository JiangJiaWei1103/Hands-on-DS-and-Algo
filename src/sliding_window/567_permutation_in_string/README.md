# [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/)
> Determine if a string `s2` contains a permutation of another string `s1`.
### Input
* `s1`: A string.
* `s2`: A string.
	* `1 <= s1.length, s2.length <= 10^4`
	* `s1` and `s2` consist of lowercase English letters.
### Output
`True` if `s2` contains a permutation of `s1`, else `False`.
### Example
```
Input: s1 = "ab", s2 = "eidbaooo"
Output: true
Explanation: s2 contains one permutation of s1 ("ba").
```
### Idea
This problem is highly similar to determining an anagram. The key point is to check if the character frequency of `s1` matches that of any substring in `s2`.
#### 1. Fixed-Size Sliding Window
To find any possible anagram of `s1` in `s2`, we use a sliding window with the length of `s1` to keep tracking the character frequency. The technique used in this implementation is to continuously decrease and increase the char frequency observed in `s1`. At each step, the sliding window shrinks from the left side and expands on the right side. If all char frequencies are cancelled out, an anagram is found.
```python
def checkInclusion(s1: str, s2: str) -> bool:
    char2freq = defaultdict(int)
    for c in s1:
        char2freq[c] += 1

    for i in range(len(s2)):
        if i < len(s1):
            char2freq[s2[i]] -= 1 
        else:
            char2freq[s2[i - len(s1)]] += 1
            char2freq[s2[i]] -= 1

        if all(freq == 0 for freq in char2freq.values()):
            return True

    return False
```
* Time complexity: $O(26 * n) \approx O(n)$
	* Each character in `s2` enters and leaves the sliding window once, hence taking $O(n)$ in total.
	* For each iteration, we need to check if all char frequencies become 0, which takes $O(26) \approx O(1)$.
* Space complexity: $O(26) \approx O(1)$
	* A hash map storing the character frequencies takes $O(1)$.
#### 2. Fixed-Size Sliding Window - Optimization
Instead of running a frequency check for each iteration, we can use another variable to record the number of matches in the current sliding window. A match occurs when the frequency of a specified character is cancelled out. For each iteration, we check if the number of matches is equal to the number of unique characters in `s1`.
```python
def checkInclusion(s1: str, s2: str) -> bool:
    char2freq = defaultdict(int)
    for c in s1:
        char2freq[c] += 1

    n_matches = 0
    for i in range(len(s2)):
        if s2[i] in char2freq:
            char2freq[s2[i]] -= 1
            if char2freq[s2[i]] == 0:
                n_matches += 1

        if i >= len(s1) and s2[i - len(s1)] in char2freq:
            char2freq[s2[i - len(s1)]] += 1
            if char2freq[s2[i - len(s1)]] == 1:
                # A match becomes invalid (freq 0 -> 1)
                n_matches -= 1

        if n_matches == len(char2freq):
            return True

    return False
```
* Time complexity: $O(n)$
	* Each character in `s2` enters and leaves the sliding window once, hence taking $O(n)$ in total.
* Space complexity: $O(26) \approx O(1)$
	* A hash map storing the character frequencies takes $O(1)$.