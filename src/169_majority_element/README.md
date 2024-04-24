# [169. Majority Element](https://leetcode.com/problems/majority-element/)
> Find the majority element in an array, which appears more than `n // 2` times.
### Input
* `nums`: An integer array.
### Output
The majority element.
### Example
```
Input: nums = [3, 2, 3]
Output: 3
```
### Idea
#### 1. Hash Map
Map each unique number to its frequency, then return the most frequent one (*i.e.,* more frequent than the threshold `n // 2`).
```python
def majorityElement(nums: List[int]) -> int:
    num2freq = defaultdict(int) 
    for num in nums:
        num2freq[num] += 1

    thres = len(nums) // 2
    for k, v in num2freq.items():
        if v > thres:
            return k
```
* Time complexity: $O(n+k)$, where $k$ is the number of unique elements
	* Traverse an array once.
	* Traverse a hash map once to find the most frequent element.
		* In the worst case, $k \approx \frac {n}{2}$.
* Space complexity: $O(k)$
	* Map each unique number to its frequency.
		* In the worst case, $k \approx \frac {n}{2}$.
#### 2. Boyer-Moore Majority Voting Algorithm
Find the majority element occurring more than `n // 2` times by one pass only.
```python
def majorityElement(nums: List[int]) -> int:
    candidate = nums[0]
    cnt = 1
    for num in nums[1:]:
        if num == candidate:
            cnt += 1
        elif cnt == 0:
            candidate = num
            cnt = 1
        else:
            cnt -= 1

    return candidate
```
* Time complexity: $O(n)$
	* Traverse an array once.
* Space complexity: $O(1)$
#### 3. Divide and Conquer
Split `nums` into left and right parts.
### References
* [Boyer-Moore Majority Voting Algorithm](https://www.geeksforgeeks.org/boyer-moore-majority-voting-algorithm/)