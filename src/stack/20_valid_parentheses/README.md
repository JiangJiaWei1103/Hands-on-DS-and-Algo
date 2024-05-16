# [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
> Determine if an input string is valid with correct bracket correspondence.
### Input
* `s`: An input string.
	* `1 <= s.length <= 10^4`
	* `s` consists of parentheses only `'()[]{}'`.
### Output
`True` if the string has the correct bracket correspondence else `False`.
### Example
```
Input: s = "()"
Output: true
```
### Idea
#### 1. Open-Only Stack
To validate if a string has a valid bracket correspondence, we can first think about what correspondences violate the rules,
1. An open parenthesis is closed by different types of close parentheses.
	* *e.g.,* `("(}")`
2. A close parenthesis appears without the corresponding open one.
	* *e.g.,* `()"}"`
3. Any open parenthesis is left without the corresponding close one.
	* *e.g.,* `([{`

We adopt an open-only stack to record open parentheses and check if the visited parenthesis violates rules stated above.
```python
def isValid(s: str) -> bool:
    open2close = {"(": ")", "{": "}", "[": "]"}
    char_stack = []
    for c in s:
        if c in open2close:
            # Record open parenthesis only
            char_stack.append(c)
        elif len(char_stack) == 0 or open2close[char_stack.pop()] != c: 
            # 1. Close without open
            # 2. Close mismatches open
            return False

    # Open without close
    return len(char_stack) == 0


def isValid(s: str) -> bool:
    """Use stack w/ dummy 0."""
    open2close = {0: None, "(": ")", "{": "}", "[": "]"}
    char_stack = [0]  # Handle close without open
    for c in s:
        if c in open2close:
            char_stack.append(c)
        elif open2close[char_stack.pop()] != c: 
            return False

    return char_stack == [0]
```
* Time complexity: $O(n)$
	* Each parenthesis is visited at most once.
* Space complexity: $O(n)$
	* A stack storing visited open parentheses takes $O(n)$.
##### Comment
* In the second implementation, a dummy `0` is used to handle conditions in which there's no open parenthesis stored in the stack for the visited closed one (*i.e.,* close without open).
* `if len(s) % 2: return False` can be put in the beginning to skip further determination because an odd-length string is definitely invalid.