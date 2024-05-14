# [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)
> Check if a string `t` is an **anagram** of a string `s`.
* An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.
### Input
* `s`: The first string.
* `t`: The second string.
	* `1 <= s.length, t.length <= 5 * 104`, each string contains at least one character.
	* `s` and `t` consist of lowercase English letters (26 unique characters).
### Output
`True` if `t` is an anagram of `s` else `False`.
### Example
```
Input: s = "anagram", t = "nagaram"
Output: true
```
### Idea
First, the solution can return `False` directly if the lengths of `s` and `t` aren't the same.
#### 1. Sorting
For two sorted strings, we just need to compare them **character by character**. If there's any mismatch, `t` can't be an anagram of `s`.
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t): 
        return False
    
    s = sorted(s)
    t = sorted(t)
    for char_s, char_t in zip(s, t):
        if char_s != char_t:
            return False

    return True
```
* Time complexity: $O(n\ log\ n)$
	* Sorting takes $O(n\ log\ n)$.
	* Character-wise comparison takes $O(n)$.
* Space complexity: $O(n)$
	* `sorted` returns a list of characters.
#### 2. Two Hash Maps
To check if `t` is an anagram of `s`, we only need to make sure the **frequency** of each character in `s` is equal to that of `t`. The most intuitive way is to maintain two hash maps recording frequencies of characters.
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t): 
        return False
    
    char2freq_s = defaultdict(int)
    char2freq_t = defaultdict(int)
    for char_s, char_t in zip(s, t):
        char2freq_s[char_s] += 1
        char2freq_t[char_t] += 1

    for char_s, freq_s in char2freq_s.items():
        if freq_s != char2freq_t[char_s]:
            return False

    return True
```
* Time complexity: $O(n)$
	* Each character in either string is visited only once.
	* Checking frequencies of recorded characters only takes $O(k) \approx O(1)$, because there are only 26 lower English letters.
* Space complexity: $O(k) \approx O(1)$, where $k$ is the number of lower English letters (26).
#### 3. One Hash Map
To further reduce the space complexity, we can instead maintain only one hash map to record **imbalance of frequencies**. That is, we increase character frequencies for one string and decrease for the other. If there exists any occurrence in one string which can't be **offset** by the other, we can make sure it's not an anagram.
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t): 
        return False

    char2freq = defaultdict(int)
    for char_s, char_t in zip(s, t):
        char2freq[char_s] += 1
        char2freq[char_t] -= 1

    for freq in char2freq.values():
        if freq != 0:
            return False
        
    return True
```
* Time complexity: $O(n)$, the same as idea 2.
* Space complexity: $O(k) \approx O(1)$, where $k$ is the number of lower English letters (26).
	* It's a little bit less than idea 2, because we only need to maintain one hash map.
#### 4. Frequency Table
Another way to record character frequencies is to use a fixed-size array, because we know all the possibilities existing in strings. The index can be derived based on the ASCII of the character.
```python
def isAnagram(s: str, t: str) -> bool:
    if len(s) != len(t): 
        return False

    char_freqs = [0] * 26
    for char_s, char_t in zip(s, t):
        char_freqs[ord(char_s)-97] += 1
        char_freqs[ord(char_t)-97] -= 1

    for freq in char_freqs: 
        if freq != 0:
            return False
        
    return True
```
* Time complexity: $O(n)$, the same as idea 3.
* Space complexity: $O(k) \approx O(1)$, the same as idea 3.