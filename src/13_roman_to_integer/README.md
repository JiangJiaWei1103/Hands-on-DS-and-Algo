# [13. Roman to Integer](https://leetcode.com/problems/roman-to-integer/)
> Convert a roman numeral to an integer.
### Input
* `s`: A roman numeral containing characters `('I', 'V', 'X', 'L', 'C', 'D', 'M')`.
### Output
The integer representation of a roman numeral.
### Example
```
Input: s = "III"
Output: 3
```
### Idea
#### 1. Traversal with Sign
Traverse a roman numeral and dynamically determine the sign. Take backward as an example,
1. **Add** the visited value if its absolute value is **greater than or equal to** the previous.
2. **Subtract** the visited value if its absolute value is **less than** the previous.
```python
def romanToInt(s: str) -> int:
    """Backward."""
    symb2val = {
        "I": 1, "V": 5, "X": 10, "L": 50,
        "C": 100, "D": 500, "M": 1000
    }

    cumsum = 0
    prev = 0
    sign = 1
    for i in range(len(s)-1, -1, -1):
        val = symb2val[s[i]]
        sign = 1 if val >= prev else -1
        cumsum += val * sign
        prev = val
    
    return cumsum


def romanToInt(s: str) -> int:
    """Forward."""
    symb2val = {
        "I": 1, "V": 5, "X": 10, "L": 50,
        "C": 100, "D": 500, "M": 1000
    }

    cumsum = 0
    for i in range(0, len(s)-1):
        if symb2val[s[i]] >= symb2val[s[i+1]]:
            cumsum += symb2val[s[i]]
        else:
            cumsum -= symb2val[s[i]]
    cumsum += symb2val[s[-1]] 

    return cumsum
```
* Time complexity: $O(n)$
	* Traverse a roman numeral once.
*  Space complexity: $O(1)$
	* The hash map stores a fixed set of symbol-value pairs.