# [88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)
> Merge two integer arrays sorted in **non-decreasing order** into another single array also sorted in **non-decreasing order**. Note that merging should be done in `nums1` **in-place**.
### Input
* `nums1`: The first integer array with length `m + n`, where the last `n` elements are dummy. 
* `m`: Length of `nums1`. 
* `nums2`: The second integer array.
* `n`: Length of `nums2`. 
### Output
The merged integer array sorted in non-decreasing order.
### Example
```
Input: nums1 = [1, 2, 3, 0, 0, 0], m = 3, nums2 = [2, 5, 6], n = 3
Output: [1, 2, 2, 3, 5, 6]
Explanation: The arrays we are merging are [1, 2, 3] and [2, 5, 6].
The result of the merge is [1 ,2 ,2 ,3 , 5, 6] with the underlined elements coming from nums1.
```
### Idea
#### 1. Slice Assignment then Sort
Note that this is not a solution!
```python
def merge(nums1: List[int], m: int, nums2: List[int], n: int) -> None:
    if n > 0:
        nums1[-n:] = nums2
        if m > 0:
            nums1.sort()
```
* Time complexity: $O((m+n)\ log\ (m+n))$
	* Sorting takes $O((m+n)\ log\ (m+n))$.
	* Assigning `nums2` to the tail slice of `nums1` takes another $O(n)$.
* Space complexity: $O(1)$
#### 2. Two Pointers Backward Comparison
Use two pointers to compare the current values of two lists from the end.
1. If `nums2` is completely traversed first, the process is done.
2. If `nums1` is completely traversed first, the remaining elements in `nums2` should be assigned to `nums1`.
```python
def merge(nums1: List[int], m: int, nums2: List[int], n: int) -> None:
    p, q = m - 1, n - 1
    cur = len(nums1) - 1  # Position to write the winner
    while p != -1 and q != -1:
        # Continue comparison if both have remaining elements
        if nums2[q] >= nums1[p]:
            nums1[cur] = nums2[q]
            q -= 1
        else:
            nums1[cur] = nums1[p]
            p -= 1
        cur -= 1

    while q != -1:
        nums1[cur] = nums2[q]
        q -= 1
        cur -= 1        
    
    
def merge(nums1: List[int], m: int, nums2: List[int], n: int) -> None:
    """Simplified version."""
    p, q = m - 1, n - 1
    cur = len(nums1) - 1  # Position to write the winner
    while q != -1:
        if p != -1 and nums1[p] > nums2[q]:
            nums1[cur] = nums1[p]
            p -= 1
        else:
            nums1[cur] = nums2[q]
            q -= 1
        cur -= 1
```
* Time complexity: $O(m+n)$
	* `nums1` and `num2` are traversed once.
* Space complexity: $O(1)$