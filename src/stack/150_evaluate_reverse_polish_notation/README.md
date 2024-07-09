# [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
> Evaluate an arithmetic expression in a **reverse polish notation**.
* The division between two integers always **truncates toward zero**.
* There will not be any division by zero.
* The answer and all the intermediate calculations can be represented in a **32-bit** integer.
### Input
* `tokens`: A string array containing operands and operators.
	* `1 <= tokens.length <= 10^4`
	* `tokens[i]` is either an operator: `"+"`, `"-"`, `"*"`, or `"/"`, or an integer in the range `[-200, 200]`.
### Output
Evaluated value of  the expression.
### Example
```
Input: tokens = ["2", "1", "+", "3", "*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```
### Idea
#### 1. Operand Stack
Following the rule of a reverse polish notation, we can easily find how **operands** interact with the currently visited **operator**. The basic concept is to evaluate the intermediate expression using the top two operands in the stack with the visited operator, as illustrated below. Due to LIFO property of a stack, we should note the ordering of operand for operators without **commutative property** (*i.e.,* `-` and `/`).
![[150_evaluate_reverse_polish_notation.drawio.png]]
```python
def evalRPN(tokens: List[str]) -> int:
    oprts = set(["+", "-", "*", "/"])
    oprd_stack = []
    for token in tokens:
        if token in oprts:
            # Evaluate intermediate expression with the visited operator
            oprd2 = oprd_stack.pop()
            oprd1 = oprd_stack.pop()
            tmp_val = eval(f"int({oprd1}{token}{oprd2})")
            oprd_stack.append(tmp_val)
        else:
            # Push the visited operand
            oprd_stack.append(token)

    return int(oprd_stack[0])


def evalRPN(tokens: List[str]) -> int:
    evals = {
        "+": lambda a, b: b + a,
        "-": lambda a, b: b - a,
        "*": lambda a, b: b * a,
        "/": lambda a, b: int(b / a),
    }

    oprd_stack = []
    for token in tokens:
        if token in evals:
            tmp_val = evals[token](oprd_stack.pop(), oprd_stack.pop())
            oprd_stack.append(tmp_val)
        else:
            oprd_stack.append(int(token))

    return oprd_stack[0]
```
* Time complexity: $O(n)$
	* Visiting each token of the entire token list takes $O(n)$.
* Space complexity: $O(n)$
	* A stack storing visited operands takes $O(n)$.
##### Comment
* Note that the python expression `//` is used to **round down** the evaluated result, which isn't equal the rounding behavior of `int()` (which takes the integer part of the value).
### Discussion
*  Can we solve the problem through recursion?