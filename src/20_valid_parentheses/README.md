# [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
> Determine if the input string is valid with correct bracket correspondence.
### Input
* `s`: Input string containing chars `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`.
### Output
`True` if string has the correct bracket correspondence else `False`.
### Example
```
Input: s = "()"
Output: true
```
### Idea
#### 1. Open-Only Stack
Use stack to record the **open** parenthesis and check if the visited closed one is correctly paired with the top of the stack.
```python
def isValid(s: str) -> bool:
    open2close = {"(": ")", "{": "}", "[": "]"}
    char_stack = []
    for c in s:
        if c in open2close:
            char_stack.append(c)
        elif len(char_stack) == 0 or open2close[char_stack.pop()] != c: 
            return False

    return len(char_stack) == 0


def isValid(s: str) -> bool:
    """Use stack w/ dummy 0."""
    open2close = {0: None, "(": ")", "{": "}", "[": "]"}
    char_stack = [0]  # Close type can't be append
    for c in s:
        if c in open2close:
            char_stack.append(c)
        elif open2close[char_stack.pop()] != c: 
            return False

    return char_stack == [0]
```
* Time complexity: $O(n)$
	* Traverse string at most once.
* Space complexity: $O(n)$
	* Use stack to store open parentheses.
##### Comment
* In the second implementation, a dummy `0` is used to handle conditions in which there's no open parenthesis stored in the stack for the visited closed one.
* `if len(s) % 2: return False` can be put in the beginning to skip further determination because odd-length string is definitely invalid.