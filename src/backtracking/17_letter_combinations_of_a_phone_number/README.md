# [17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)
> Find all letter combinations of a phone number.
### Input
* `digits`: A string.
	* `0 <= digits.length <= 4`
	* `digits[i]` is a digit in the range `['2', '9']`.
### Output
All letter combinations of the phone number.
### Example
```
Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```
### Idea
The solution is quite straightforward, because we must enumerate all possible combinations of the given phone number. 
#### 1. Backtracking DFS
The solution is actually a full DFS if we consider that each number has already had its own letter mapping. From another perspective, we can say that the constraint is that each number can only be mapped to a limited set of letters.

Specifically, we create a hash map to store the mapping from each number to it's corresponding letters. Then, we keep extend the combination as a tree until the mapping is done (i.e., the length of the combination is equal to the length of `digits`).
```python
def letterCombinations(digits: str) -> List[str]:
    def _dfs(comb: str, i: int) -> None:
        if len(comb) == len(digits):
            res.append(comb)
            return

        for letter in num2letter[digits[i]]:
            _dfs(comb + letter, i + 1)

    num2letter = {
        "2": ["a", "b", "c"],
        "3": ["d", "e", "f"],
        "4": ["g", "h", "i"],
        "5": ["j", "k", "l"],
        "6": ["m", "n", "o"],
        "7": ["p", "q", "r", "s"],
        "8": ["t", "u", "v"],
        "9": ["w", "x", "y", "z"]
    }
    res = []
    if len(digits) > 0:
        _dfs("", 0)

    return res
```
* Time complexity: $O(n * 4^n)$, where $n$ is the number of digits in the string.
	* The worst case happens when each number has 4 possible mappings, which can be represented as a 4-ary tree of height $n$.
	* Copying each mapped string `comb` takes $O(n)$.
* Space complexity: $O(n)$
	* The depth of call stack takes $O(n)$.
##### Comment
 * We can switch to an iterative approach. Just keep expanding with the combinations obtained so far. `res` itself can be viewed as a simulation of a queue.
```python
def letterCombinations(digits: str) -> List[str]:
    if len(digits) == 0:
        return []
    
    num2letter = {
        "2": ["a", "b", "c"],
        "3": ["d", "e", "f"],
        "4": ["g", "h", "i"],
        "5": ["j", "k", "l"],
        "6": ["m", "n", "o"],
        "7": ["p", "q", "r", "s"],
        "8": ["t", "u", "v"],
        "9": ["w", "x", "y", "z"]
    }
    res = [""]
    for num in digits:
        tmp = [] 
        for comb in res:
            for c in num2letter[num]:
                tmp.append(comb + c)

        res = tmp

    return res
```
