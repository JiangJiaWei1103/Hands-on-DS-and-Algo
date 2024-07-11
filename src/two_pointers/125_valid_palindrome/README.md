# [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
> Check if a string is a palindrome.
* A phrase is a **palindrome** if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.
### Input
* `input`: A string.
	* `1 <= s.length <= 2 * 10^5`
	* `s` consists only of printable ASCII characters.
### Output
`True` if the string is a palindrome else `False`.
### Example
```
Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.
```
### Idea
#### 1. Brute-force
The most intuitive method is to clean the string first and only retain the characters that are either letters or numbers. After cleaning, we compare the string with its own reversed counterpart.
```python
def isPalindrome(s: str) -> bool:
    # Clean the string forward
    s_f = [char.lower() for char in s if char.isalnum()]
    
    # Construct a backward view
    s_b = []
    for i in range(len(s_f) - 1, -1, -1):
        s_b.append(s_f[i])

    return s_f == s_b
```
* Time complexity: $O(n)$
	* Both forward and backward traversal take $O(n)$.
	* The final element-wise comparison also takes $O(n)$.
* Space complexity: $O(n)$
	* Two lists storing two views of the cleaned string take $O(n)$.
#### 2. Two Pointers - Clean First
For a cleaned string, we can use two pointers to compare left and right characters. If there's a mismatch, the string is definitely not a palindrome. The process is like placing a mirror in the middle of the string.
```python
def isPalindrome(s: str) -> bool:
    cleaned_s = [char.lower() for char in s if char.isalnum()]
    l, r = 0, len(cleaned_s) - 1
    while l < r:
        if cleaned_s[l] != cleaned_s[r]:
            return False

        l += 1
        r -= 1

    return True
```
* Time complexity: $O(n)$
	* Both cleaning and comparison take $O(n)$.
* Space complexity: $O(n)$
	* A list storing the cleaned string take $O(n)$.
#### 3. Two Pointers
We can further reduce the space complexity by **cleaning the string on the fly**, instead of preprocessing. The core concept is to iteratively find the next **comparable** character (*i.e.,* either a letter or a number).
```python
def isPalindrome(s: str) -> bool:
    l, r = 0, len(s) - 1
    while l < r:
        if not s[l].isalnum():
            l += 1
        elif not s[r].isalnum():
            r -= 1
        else:
            if s[l].lower() != s[r].lower():
                return False
            
            l += 1
            r -= 1
    
    return True
```
* Time complexity: $O(n)$
	* Visiting each character in the string takes $O(n)$ in total.
* Space complexity: $O(1)$
##### Comment
* We can also use inner while loops to directly move both left and right pointers to the next valid positions to compare. However, the readability is poor.