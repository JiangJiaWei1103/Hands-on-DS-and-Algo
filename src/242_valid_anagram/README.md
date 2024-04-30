# [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)
> Check if string `t` is an **anagram** of string `s`.
* An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.
### Input
* `s`: The first string.
* `t`: The second string.
### Output
`True` if `t` is an anagram of `s` else `False`.
### Example
```
Input: s = "anagram", t = "nagaram"
Output: true
```
### Idea
First, the solution can directly return `False` when the base case, `len(s) != len(t)`, holds.
#### 1. Comparing Chars after Sorting
Sort two strings, then return `False` when visited characters are different.
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False

    s = sorted(s)
    t = sorted(t)
    for cs, ct in zip(s, t):
        if cs != ct:
            return False
    
    return True
```
* Time complexity: $O(n\ log\ n)$
	* Sorting takes $O(n\ log\ n)$.
	* Character-wise comparison takes additional $O(n)$.
* Space complexity: $O(n)$
	* `sorted` returns a list of characters.
#### 2. Hash Map
Map each character to its frequency in `s` (with `+`), and offset the corresponding frequency in `t` (with `-`). If `t` is an anagram of `s`, all frequencies should be `0`'s.
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    
    char2freq = defaultdict(int)
    for cs, ct in zip(s, t):
        char2freq[cs] += 1
        char2freq[ct] -= 1

    for freq in char2freq.values():
        if freq != 0: 
            return False

    return True
```
* Time complexity: $O(n)$
	* Traverse `t` and `s` once.
	* The frequency check takes at most 26 iterations.
* Space complexity: $O(1)$
	* The hash map takes at most 26 key-value pairs.
#### 3. Frequency Table
Use a list with length 26 to record the frequency of each character. The index is derived based on the ASCII of the character.
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False

    freqs = [0 for _ in range(26)]
    for cs, ct in zip(s, t):
        freqs[ord(cs)-97] += 1
        freqs[ord(ct)-97] -= 1

    for freq in freqs:
        if freq != 0:
            return False

    return True
```
 * Time complexity: $O(n)$
	* Traverse `t` and `s` once.
	* The frequency check takes at most 26 iterations.
* Space complexity: $O(1)$
	* The list has length 26 for 26 unique characters.