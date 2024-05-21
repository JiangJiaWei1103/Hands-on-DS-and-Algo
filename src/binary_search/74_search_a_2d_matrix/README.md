# [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)
> Determine if `target` exists in a 2D integer matrix.
- Each row is sorted in **non-decreasing order**.
- The first integer of each row is greater than the last integer of the previous row.
### Input
* `matrix`: A 2D integer matrix.
	* `m == matrix.length`
	* `n == matrix[i].length`
	* `1 <= m, n <= 100`
	* `-10^4 <= matrix[i][j] <= 10^4`
* `target`: A target integer to find.
	* `-10^4 <= target <= 10^4`
### Output
`True` if `target` is found else `False`.
### Example
```
Input: matrix = [
	[1, 3, 5, 7],
	[10, 11, 16, 20],
	[23, 30, 34, 60]
], target = 3
Output: true
```
### Idea
#### 1. Flattened Binary Search
The most intuitive solution is to flatten the 2D matrix into a 1D array first. Then, we can do a normal binary search. However, the flattening operation takes $O(mn)$ in both time and space complexities, which isn't acceptable.
#### 2. Binary Search x `m`
Instead, we can run binary search **for each row**. Note that this solution is still not acceptable, because we need a solution with $O(log\ (mn))$ time complexity.
```python
def searchMatrix(matrix: List[List[int]], target: int) -> bool:
    def _bs_one_row(row: List[int], target: int) -> bool:
        l, r = 0, n - 1
        while l <= r:
            mid = (l + r) // 2
            if row[mid] == target:
                return True
            elif row[mid] < target:
                l = mid + 1
            else:
                r = mid - 1

        return False
        
    n = len(matrix[0])
    for i in range(len(matrix)):
        if _bs_one_row(matrix[i], target):
            return True

    return False
```
* Time complexity: $O(m\ log\ n)$
	* Binary search for each row takes $O(log\ n)$.
* Space complexity: $O(1)$
#### 3. Pseudo-Flattened Binary Search
Instead of explicitly flattening the 2D array or running binary search for each row separately, we can just run binary search on a **flattened view** of this matrix. The key difference with 1D binary search is that we need to **locate the exact position of the middle element**. To be concrete, we need to **find the index pair `(i, j)` of the middle element**.
```python
def searchMatrix(matrix: List[List[int]], target: int) -> bool:
    def _mid2pos(mid: int) -> Tuple[int, int]:
        """Convert middle pointer to the index pair."""
        return mid // n, mid % n
        # or
        # return divmod(mid, n)

    m, n = len(matrix), len(matrix[0])
    l, r = 0, m * n - 1
    while l <= r:
        mid = (l + r) // 2
        i, j = _mid2pos(mid)

        if matrix[i][j] == target:
            return True
        elif matrix[i][j] < target:
            l = mid + 1
        else:
            r = mid - 1

    return False
```
* Time complexity: $O(log\ (mn))$
	* This algorithm can be seen of running binary search on a **flattened view** of the 2D matrix, which takes $O(log\ (mn))$ for $mn$ total elements.
* Space complexity: $O(1)$
#### 4. Separate BS - Row then Column
### Discussion
* Did you observe that the 2D matrix can be viewed as a tree-like structure?
