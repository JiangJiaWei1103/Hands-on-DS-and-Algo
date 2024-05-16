# [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)
> Group anagrams in a string list together.
* An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.
### Input
* `strs`: An array of strings.
	* `1 <= strs.length <= 10^4`
	* `0 <= strs[i].length <= 100`
	* `strs[i]` consists of lowercase English letters.
### Output
A list containing lists of strings, in which anagrams are grouped together.
### Example
```
Input: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
Output: [["bat"], ["nat", "tan"], ["ate", "eat", "tea"]]
```
### Idea
#### 1. Brute-force (TLE)
The most intuitive way is to do a pairwise comparison between the current string and strings in the **existing** groups. There are two conditions summarized as follows,
1. If the current string `s` is an anagram of the first string `t` in $group_i$, append `s` to $group_i$.
2. If `s` isn't an anagram of any existing group, create a new group for `s`.
```python
def groupAnagrams(strs: List[str]) -> List[List[str]]:
    def _is_anagram(str1: str, str2: str) -> bool:
        if len(str1) != len(str2):
            return False

        char2freq = defaultdict(int)
        for char1, char2 in zip(str1, str2):
            char2freq[char1] += 1
            char2freq[char2] -= 1

        for freq in char2freq.values():
            if freq != 0:
                return False

        return True

    gps = []
    for str1 in strs:
        hit = False
        for i, gp in enumerate(gps):
            if _is_anagram(str1, gp[0]):
                hit = True
                gps[i].append(str1)
                break
        
        if not hit:
            # Create a new group for current string
            gps.append([str1])
    
    return gps
```
* Time complexity: $O(n^2k)$, where $n$ is the number of strings in `strs` and $k$ is the longest string length.
	* Each string should compare with all existing groups, the worst case is that each string isn't an anagram of all the others. Hence, it would take $O(n^2)$ to do the pairwise comparison.
	* Each anagram checking takes $O(k)$.
* Space complexity: $O(nk)$
	* A list storing the grouped result takes $O(nk)$.
#### 2. Hash Map - Sorted String
To avoid pairwise comparison, we can leverage the property of anagrams; that is, two **sorted** strings will be the same if they're anagrams of each other. Hence, **sorting**can be seen as a hash function in this solution.
```python
def groupAnagrams(strs: List[str]) -> List[List[str]]:
    sorted_str2gp = defaultdict(list) 
    for s in strs:
        # Hash current string 
        sorted_str = "".join(sorted(s))
        # or 
        # sorted_str = tuple(sorted(s)) 

        # Strings with the same hash values are grouped together
        sorted_str2gp[sorted_str].append(s)
    gps = list(sorted_str2gp.values())

    return gps
```
 * Time complexity: $O(nk\ log\ k)$
	* `strs` is traversed only once, which takes $O(n)$.
	* Sorting for each string takes $O(k\ log\ k)$.
	* Creation of the list containing anagram groups takes another $O(n)$.
* Space complexity: $O(nk)$
	* A hash map recording groups takes $O(nk)$.
	* A list storing the grouped result takes $O(nk)$.
##### Comment
* To further reduce the space complexity, we can map a sorted string to its group index, instead of the actual groups.
```python
def groupAnagrams(strs: List[str]) -> List[List[str]]:
    gps = []
    sorted_str2gp_idx = {}
    gp_idx = 0
    for s in strs:
        sorted_str = "".join(sorted(s))
        if sorted_str not in sorted_str2gp_idx:
            # Create a new group
            sorted_str2gp_idx[sorted_str] = gp_idx
            gps.append([s])
            gp_idx += 1
        else:
            gps[sorted_str2gp_idx[sorted_str]].append(s)

    return gps
```
#### 3. Hash Map - Char Frequency
Instead of sorting, one faster way is to use **character frequencies** (*i.e.,* counts) as a new hash function. The concept is to count the occurrences of each character, then convert this frequency information into a **hashable** (*i.e.,* **immutable** in python) type.
```python
def groupAnagrams(strs: List[str]) -> List[List[str]]:
    def _hash_str(s: str) -> str:
        char_freqs = [0] * 26
        for char in s:
            char_freqs[ord(char)-97] += 1
        hash_code = "".join([f"{chr(i+97)}{char_freq}" for i, char_freq in enumerate(char_freqs)])

        return hash_code
        
    sorted_str2gp = defaultdict(list) 
    for s in strs:
        hash_code = _hash_str(s)
        sorted_str2gp[hash_code].append(s)
    gps = list(sorted_str2gp.values())

    return gps
```
 * Time complexity: $O(nk)$
	* `strs` is traversed only once, which takes $O(n)$.
	* Counting char occurrences for each string takes $O(k)$.
	* Converting char occurrences into a hashable type takes $O(k)$.
	* Creation of the list containing anagram groups takes another $O(n)$.
* Space complexity: $O(nk)$
	* A hash map recording groups takes $O(nk)$.
	* A list storing the grouped result takes $O(nk)$.
