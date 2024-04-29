# [118. Pascal's Triangle](https://leetcode.com/problems/pascals-triangle/)
> Return the first `numRows` of Pascal's triangle.
### Input
* `numRows`: Number of rows in a Pascal's triangle.
### Output
The Pascal's triangle containing `numRows` rows.
### Example
```
Input: numRows = 5
Output: [[1], [1, 1], [1, 2, 1], [1, 3, 3, 1], [1, 4, 6, 4, 1]]
```
### Idea
#### 1. Iterative
Iteratively construct the current row based on the previous one.
```python
def generate(numRows: int) -> List[List[int]]:
    triangle = [[1]]
    for r in range(1, numRows):
        prev = triangle[-1]

        # Construct current row
        cur = [1]
        for c in range(1, r):
            cur.append(prev[c] + prev[c-1])
        cur.append(1)

        triangle.append(cur)
    
    return triangle
```
* Time complexity: $O(n^2)$
	* Use nested loops to construct the entire triangle.
* Space complexity: $O(n^2)$
	* Use a 2D array to store the triangle.
#### 2. Recursive
Recursively construct the triangle with the base case, hitting the first row.
```python
def generate(numRows: int) -> List[List[int]]:
    # Base case
    if numRows == 1:
        return [[1]]

    triangle = generate(numRows-1)

    prev = triangle[-1]
    cur = [1]
    for c in range(1, numRows-1):
        cur.append(prev[c] + prev[c-1])
    cur.append(1)
    triangle.append(cur)

    return triangle
```
* Time complexity: $O(n^2)$
	* Go through all $\frac{n^2}{2}$ elements in the triangle.
* Space complexity: $O(n^2)$
	* Use a 2D array to store the triangle.
	* The depth of the call stack is at most `numRows`.
#### 3. Auxiliary `0`'s on Both Sides
Add `0`'s to left and right sides and add up these two temporary arrays.
[![118-pascals-triangle-drawio.png](https://i.postimg.cc/28DZDwVj/118-pascals-triangle-drawio.png)](https://postimg.cc/F1TRb0yw)
```python
def generate(numRows: int) -> List[List[int]]:
    triangle = [[1]]
    for r in range(1, numRows):
        prev = triangle[-1]
        triangle.append(list(map(lambda l, r: l+r, [0]+prev, prev+[0])))
    
    return triangle
```
* Time complexity: $O(n^2)$
* Space complexity: $O(n^2)$
	* Temporary arrays need additional space.
#### 4. Binomial Coefficients

### References
* [Pascal's triangle](https://en.wikipedia.org/wiki/Pascal%27s_triangle)