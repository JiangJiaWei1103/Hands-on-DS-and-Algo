# [387. First Unique Character in a String](https://leetcode.com/problems/first-unique-character-in-a-string/)
> Find the first **non-repeating** character in a string.
### Input
* `s`: Input string. 
### Output
The index of the first **non-repeating** character if exists else `-1`.
### Example
```
Input: s = "leetcode"
Output: 0
```
### Idea
#### 1. Hashmap
Map each character to its index and frequency.
```python
def firstUniqChar(s: str) -> int:
    if len(s) == 1: return 0

    char2num = {}
    for i, char in enumerate(s):
        if char not in char2num:
            char2num[char] = [i, 1]
        else:
            char2num[char][1] += 1
    
    for k, v in char2num.items():
        if v[1] == 1:
            return v[0]
    return -1
```
* Time complexity: $O(n)$
	* Traverse the string only once.
	* The second traverse takes at most 26 times (*i.e.,* number of English letters).
*  Space complexity: $O(1)$
	* Use a hashmap with the number of key-val pairs up to 26.
##### Comment
* If one character is not unique, we can just ignore its index information and overwrite it.