# [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
> Find the length of the **longest** substring without repeating characters in a string.
* A **substring** is a contiguous **non-empty** sequence of characters within a string.
### Input
* `s`: A string.
	* `0 <= s.length <= 5 * 10^4`
	* `s` consists of English letters, digits, symbols and spaces.
### Output
Length of the longest substring without repeating characters.
### Example
```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```
### Idea
#### 1. Brute-force
The most intuitive solution is to find the longest substring without duplicated characters **for each character** in `s`. Also, we stop expanding the substring if any duplicated character is encountered.
```python
def lengthOfLongestSubstring(s: str) -> int:
    max_len = 0
    for i, c in enumerate(s):
        char_set = set([c])
        length = 1
        for j in range(i + 1, len(s)):
            if s[j] in char_set:
                break
            char_set.add(s[j])
            length += 1
        max_len = max(max_len, length)

    return max_len
```
* Time complexity: $O(n^2)$
	* Finding the longest substring for each character takes $O(n^2)$.
* Space complexity: $O(n)$
	* A set storing unique characters in current substring takes $O(n)$.
#### 2. Sliding Window
Instead of finding the longest substrings for each character iteratively, we can think of the whole process as expanding and shrinking a window (*i.e.,* a substring) alternatively,
1. Keep expanding the window if no any duplicate is encountered.
2. When a duplicated character is met, keep shrinking the window **until this specific duplicated character is removed**.
Concretely speaking, we use a set to determine if any duplicate is encountered.
```python
def lengthOfLongestSubstring(s: str) -> int:
    char_set = set()
    l, r = 0, 0
    max_len = 0
    while r < len(s):
        if s[r] in char_set:
            # Shrink the window
            char_set.remove(s[l])
            l += 1

        # Exapnd the window
        char_set.add(s[r])

        # Update the maximum length
        max_len = max(max_len, r - l + 1)
        r += 1
    
    return max_len


def lengthOfLongestSubstring(s: str) -> int:
    char_set = set()
    l = 0
    max_len = 0
    for r in range(len(s)):
        while s[r] in char_set:
            # Shrink the window
            char_set.remove(s[l])
            l += 1

        # Exapnd the window
        char_set.add(s[r])

        # Update the maximum length
        max_len = max(max_len, r - l + 1)
    
    return max_len
```
* Time complexity: $O(n)$
	* Each character is covered by the window only once (*i.e.,* add to and remove from the window).
* Space complexity: $O(n)$
	* A set storing unique characters in current substring takes $O(n)$.
##### Comment
* Here demonstrates another implementation using a hash map, which maps each character to **the latest visited position (*i.e.,* index)**. This implementation doesn't require any deletion operation. However,  note that we need to make sure the left pointer (used to shrink the window) will never go back.
```python
def lengthOfLongestSubstring(s: str) -> int:
    char2idx = {}
    l, r = 0, 0
    max_len = 0
    while r < len(s):
        char = s[r]
        if char in char2idx and char2idx[char] + 1 > l:
            l = char2idx[char] + 1
        
        char2idx[char] = r
        max_len = max(max_len, r - l + 1)
        r += 1

    return max_len
```