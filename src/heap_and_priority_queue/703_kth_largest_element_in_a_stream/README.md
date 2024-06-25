# [703. Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/)
> Design a class to find the `kth` largest element in a stream.
### Input
* `k`: An integer indicating `kth`.
	* `1 <= k <= 10^4`
* `nums`: An integer array.
	* `0 <= nums.length <= 10^4`
	* `-10^4 <= nums[i] <= 10^4`
* `val`:  An integer to add.
	* `-10^4 <= val <= 10^4`
### Output
The `kth` largest element at each snapshot.
### Example
```
Input
["KthLargest", "add", "add", "add", "add", "add"]
[[3, [4, 5, 8, 2]], [3], [5], [10], [9], [4]]
Output
[null, 4, 5, 5, 8, 8]

Explanation
KthLargest kthLargest = new KthLargest(3, [4, 5, 8, 2]);
kthLargest.add(3);   // return 4
kthLargest.add(5);   // return 5
kthLargest.add(10);  // return 5
kthLargest.add(9);   // return 8
kthLargest.add(4);   // return 8
```
### Idea
#### 1. Brute-force - Sorting
To find the `kth` largest element in an array, the most intuitive solution is to sort the array first, which is followed by a direct retrieval. The problem is that sorting needs to be performed every time a new element is added.
```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]) -> None:
        self.k = k
        self.nums = nums

    def add(self, val: int) -> int:
        self.nums.append(val)
        self.nums.sort(reverse=True)

        return self.nums[self.k-1]
```
* Time complexity:
	* `__init__`: $O(1)$
	* `add`: $O(m*n\ log\ n)$, where $m$ is the number of `add` calls.
		* Every time a new value is added, the sorting operation takes $O(n\ log\ n)$.
* Space complexity: $O(n)$
	* An integer array storing the whole data stream takes $O(n)$.
#### 2. K-Element Min Heap
Considering that the only operation to perform for the data stream is to retrieve the `kth` element, we can keep only the top-k elements. With the heap property, we also make sure that retrieving the `kth` element can be done in $O(1)$. The process is summarized as follows,
1. Construct the min heap containing only the top-k elements.
	* Heapify the input array.
	* Pop all elements except for the top-k ones.
2. To add a new element, perform
	* Add the element to the heap.
	* Pop the smallest element if the number of elements in the heap exceed `k`.
```python
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = nums
        heapq.heapify(self.nums)

        while len(self.nums) > k:
            _ = heapq.heappop(self.nums)

    def add(self, val: int) -> int:
        heapq.heappush(self.nums, val)
        if len(self.nums) > self.k:
            heapq.heappop(self.nums)

        return self.nums[0]
```
* Time complexity:
	*  `__init__`: $O(n\ log\ n)$
		* Running heapify on the input array takes $O(n\ log\ n)$.
		* Popping elements except for the top-k ones takes $O((n-k)\ log\ n)$, and $O(n\ log\ n)$ for the worst case ($i.e.,$ $n \gg k$).
	* `add`: $O(m\ log\ k)$, where $m$ is the number of `add` calls.
		* Every time a new value is added, it takes $O(2\ log\ k) \approx O(log\ k)$ to complete the push and pop operations.
* Space complexity: $O(k)$
	* A min heap storing only the top-k elements takes $O(k)$.
