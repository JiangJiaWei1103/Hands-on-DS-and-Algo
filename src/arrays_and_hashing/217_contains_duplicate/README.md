# [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)
> Check if an integer array contains any duplicate.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 10^5`, containing at least one element.
	* `-10^9 <= nums[i] <= 10^9`
### Output
`True` if there's any duplicate else `False`.
### Example
```
Input: nums = [1, 2, 3, 1]
Output: true
```
### Idea
#### 1. Brute-force (TLE)
The most intuitive way is to check whether there exists any duplicate for each element in the array. Hence, a **nested loop** can be used to solve the question.
```python
def containsDuplicate(nums: List[int]) -> bool:
    n_nums = len(nums)
    for i in range(n_nums):
        for j in range(i+1, n_nums):
            if nums[i] == nums[j]:
                return True
    
    return False
```
* Time complexity: $O(n^2)$
	* A nested loop is used to check duplicates for each element.
* Space complexity: $O(1)$
#### 2. Sorting
For a sorted array, duplicates can be found by linearly traversing the array and checking whether **neighboring elements** are the same or not.
```python
def containsDuplicate(nums: List[int]) -> bool:
    nums.sort()
    for i in range(0, len(nums)-1):
        if nums[i] == nums[i+1]:
            return True

    return False
```
* Time complexity: $O(n\ log\ n)$
	* Sorting an array takes $O(n\ log\ n)$.
	* Linearly traversing an array takes $O(n)$.
* Space complexity: $O(1)$
#### 3. Hash Map
If any duplicate exists for one element, it means that its **frequency** is greater than one. Hence, we can use a hash map to record the number of occurrences of each element. Then, we check if there's any element with frequency greater than one.
```python
def containsDuplicate(nums: List[int]) -> bool:
    num2freq = defaultdict(int)
    for num in nums:
        num2freq[num] += 1

    for num, freq in num2freq.items():
        if freq > 1:
            return True
        
    return False
```
* Time complexity: $O(n)$
	* Recording element frequencies takes $O(n)$.
	* Checking duplicates also takes $O(n)$.
* Space complexity: $O(n)$
	* A hash map recording frequencies takes $O(n)$.
#### 4. Set
To avoid the separate duplicate checking like idea 3 above, we can use a set to record visited elements and determine if the element under checking has been visited (*i.e.,* has been recorded in the set).
```python
def containsDuplicate(nums: List[int]) -> bool:
    visited = set()
    for num in nums:
        if num in visited:
            return True
        visited.add(num)

    return False


def containsDuplicate(nums: List[int]) -> bool:
    visited = {}
    for num in nums:
        if num in visited:
            return True
        visited[num] = True

    return False
```
* Time complexity: $O(n)$
	* Each element is visited at most once.
* Space complexity: $O(n)$
	* A set tracking the status of visiting takes $O(n)$.
##### Comment
* Another set approach can be done in one line `len(nums) != len(set(nums))`. Note that the built-in set construction takes $O(n)$ itself.