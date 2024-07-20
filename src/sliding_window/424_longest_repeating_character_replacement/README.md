# [424. Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
> Find the length of the longest substring containing **the same letter** by replacing any character with another one for at most `k` times.
### Input
* `s`: A string.
	* `1 <= s.length <= 10^5`
	* `s` consists of only uppercase English letters (26 in total).
* `k`: An integer representing the most times to perform replacement.
	* `0 <= k <= s.length`
### Output
The length of the longest substring containing the same letter after replacement.
### Example
```
Input: s = "ABAB", k = 2
Output: 4
Explanation: Replace the two 'A's with two 'B's or vice versa.
```
### Idea
#### 1. Sliding Window with Hash Map
For a contiguous substring to be valid (*i.e.,* all characters in the current substring are the same after replacement), we need to make sure the following condition holds,
```
substr.length - max_freq <= k
```
The procedure is summarized as follows,
1. Keep expanding the window until the substring becomes invalid (*i.e.,* replacement times isn't sufficient).
2. Shrink the window **by one** because we want to find **the longest**.

[![424.png](https://i.postimg.cc/9Mp4gbNz/424.png)](https://postimg.cc/FdYs7yvQ)

```python
def characterReplacement(s: str, k: int) -> int:
    max_len = 0
    char2freq = defaultdict(int)

    l = 0
    for r in range(len(s)):
        char2freq[s[r]] += 1

        if (r - l + 1) - max(char2freq.values()) > k:
            char2freq[s[l]] -= 1
            l += 1

        max_len = max(max_len, r - l + 1)

    return max_len


def characterReplacement(s: str, k: int) -> int:
    max_len = 0
    char2freq = defaultdict(int)

    l = 0
    for r in range(len(s)):
        char2freq[s[r]] += 1

        while (r - l + 1) - max(char2freq.values()) > k:
            char2freq[s[l]] -= 1
            l += 1

        max_len = max(max_len, r - l + 1)

    return max_len
```
* Time complexity: $O(n)$
	* Each character is added to and removed from the window at most once, hence taking $O(n)$ in total.
	* Finding the most frequent character through linear scanning takes $O(26) \approx O(1)$.
* Space complexity: $O(1)$ 
	* A hash map recording the frequencies of **finite** characters takes $O(1)$.
##### Comment
* Because the character set is finite, we can also use a fixed-length array to track frequencies.
* The second implementation uses a `while` loop inside the `for` loop to directly make the current window become valid. On the hand, a simple `if` in the first implementation lets the `for` loop go forward and continuously handle any invalid situation one step at a time.
* Note that the time and space complexity can still scale up when the input character set becomes larger.
#### 2. Sliding Window with Hash Map - Optimization
As stated in the time complexity analysis in idea 1, we need to linearly scan the hash map to find the most frequent character. However, we can use an additional variable to track this information to avoid scanning for each iteration.<br>
At the first glance, we might argue that `max_freq` becomes obsolete. However, note that the objective is to find **the longest substring**, so there's no need to keep `max_freq` up to date. To be concrete, the best answer can be defined as follows,
```
max_len = max_freq + k
```
We observe that we can find a better solution if and only if we find another greater `max_freq`. 
```python
def characterReplacement(s: str, k: int) -> int:
    max_len = 0
    char2freq = defaultdict(int)
    max_freq = 0

    l = 0
    for r in range(len(s)):
        char2freq[s[r]] += 1
        max_freq = max(max_freq, char2freq[s[r]])

        if (r - l + 1) - max_freq > k:
            char2freq[s[l]] -= 1
            l += 1

        max_len = max(max_len, r - l + 1)

    return max_len
```
*  Time complexity: $O(n)$
	* Each character is added to and removed from the window at most once, hence taking $O(n)$ in total.
* Space complexity: $O(1)$ 
	* A hash map recording the frequencies of **finite** characters takes $O(1)$.