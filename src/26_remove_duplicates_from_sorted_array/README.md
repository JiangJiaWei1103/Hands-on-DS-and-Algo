# [26. Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
> **In-place** remove the duplicated elements in `nums` and keep the **relative order** of numbers the same. Then, return the number of unique integers `k`.
### Input
* `nums`: An array of integers sorted in **non-decreasing order**.
### Output
* `k`: The number of unique integers.
* `nums` (in-place): An array containing **unique** integers at the first `k` positions.
### Idea
#### 1. Deletion with Two Pointers
Check the change of neighbor numbers and remove duplicates **in the reverse order** to guarantee indexing works properly.
```python
def removeDuplicates(nums: List[int]) -> int:
    for tail in range(len(nums) - 1, 0, -1):
        head = tail - 1
        if nums[tail] == nums[head]:
            del nums[tail]
    k = len(nums)

    return k
```
* Time complexity: $O(n^2)$
	* Traverse `nums` once explicitly.
	* Each `del`, `pop` or `remove` operation needs to know the position of the unwanted element, so a linear scan is performed.
		* The rest of the list has to be left shifted.
* Space complexity: $O(1)$ 
* [Method 3](https://leetcode.com/problems/remove-duplicates-from-sorted-array/solutions/2107606/py-all-4-methods-intuitions-walk-through-wrong-answer-explanations-for-beginners-python/) shows how to use `while` to delete elements in a forward manner.
#### 2. Deletion with Hash Map
Map the visited number to the tail index (*i.e.,* occurring latter in `nums`) and remove the duplicated element based on the tail index.
```python
def removeDuplicates(nums: List[int]) -> int:
    visited = {}
    for tail in range(len(nums) - 1, -1, -1):
        cur_num = nums[tail]
        if cur_num in visited:
            # Remove the number occurring latter in list
            del nums[visited[cur_num]]

        visited[cur_num] = tail
    k = len(nums)

    return k
```
* Time complexity: $O(n^2)$
	* Traverse `nums` once explicitly.
	* Hash map lookup for visited takes only **constant time**.
	* Each `del` takes $O(n)$. Also, left shift is performed.
	* One more check than idea 1, which iterates $n-1$ times.
*  Space complexity: $O(k)$
	* Hash map stores index information of unique integers in `nums`.
	* The worst case happens when there's no duplication in `nums`, which will take $O(n)$.
#### 3. Assignment with Hash Map
Map the unique number to the position for assignment.
```python
def removeDuplicates(nums: List[int]) -> int:
	visited = {}
    k = 0
    for num in nums:
        if num not in visited:
            visited[num] = k
            k += 1
	
	# Assign unique integers back to the first k positions
    for un, ui in visited.items():
        nums[ui] = un

    return k
```
* Time complexity: $O(n+k)$
	* Traverse `nums` once.
	* Assigning `k` elements back to `nunms` takes $O(k)$.
		* Assignment operation takes $O(1)$.
*  Space complexity: $O(k)$
	* Hash map stores index information of unique integers in `nums`.
	* The worst case happens when there's no duplication in `nums`, which will take $O(n)$.
#### 4. Replacement with Two Pointers
Use the anchor (slow pointer) to track the position to **overwrite** and the checker (fast pointer) to check the change of neighbor numbers and the number to write.
```python
def removeDuplicates(nums: List[int]) -> int:
    # The first element must be included
    # The current #unique numbers is equal to the anchor pos to write a new unique num
    k = 1
    for i in range(1, len(nums)):
        if nums[i] != nums[i - 1]:
            nums[k] = nums[i]
            k += 1

    return k
```
*  Time complexity: $O(n)$
	* Traverse `nums` once.
	* Assignment operation takes $O(1)$.
* Space complexity: $O(1)$
### Discussion: `del`, `pop` and `remove`
```python 
for tail in range(len(nums)-1, 0, -1):
	head = tail - 1

	# del
	if nums[tail] == nums[head]:
		del nums[tail]

	# pop
	if nums[tail] == nums[head]:
		_ = nums.pop(tail)

	# remove
	cur_num = nums[tail]
	if cur_num == nums[head]:
		nums.remove(cur_num)
```
In the example above, `remove` is much slower (around 40x slower for submission) than `del` and `pop`, which have the similar runtime.
### Referene
* [Remove multiple elements from a list in Python](https://www.geeksforgeeks.org/remove-multiple-elements-from-a-list-in-python/)
* [What is the Difference Between del, pop and remove in Python?](https://www.naukri.com/code360/library/what-is-the-difference-between-del-pop-and-remove-in-python)