# [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
> Find the top-k most frequent elements in an integer array. An unique answer is guaranteed.
### Input
* `nums`: An integer array.
	* `1 <= nums.length <= 10^5`
	* `-10^4 <= nums[i] <= 10^4`
* `k`: An integer.
	* `k` $\in$ `[1, the number of unique elements in the array]`
### Output
Top-k most frequent elements.
### Example
```
Input: nums = [1, 1, 1, 2, 2, 3], k = 2
Output: [1, 2]
```
### Idea
#### 1. Sorting by Num Frequency
The most intuitive solution is to record the frequency of each unique number, then sort these records by **frequency**. Finally, we can simply take the top-k most frequent elements.

```python
def topKFrequent(nums: List[int], k: int) -> List[int]:
    num2freq = defaultdict(int)
    for num in nums:
        num2freq[num] += 1

    # Sort by frequency
    num2freq = sorted(num2freq.items(), key=lambda item: item[1])
    topk = [num2freq[i][0] for i in range(-k, 0, 1)] 
    
    return topk
```
* Time complexity: $O(n\ log\ n)$
	* Record number frequencies takes $O(n)$.
	* Sorting frequency records takes $O(n\ log\ n)$.
	* Taking the top-k most frequent elements takes $O(n)$.
* Space complexity: $O(n)$
	* A hash map recording the frequency of each unique number takes $O(n)$.
#### 2. Heap
Instead of sorting all unique elements, we can **heapify** elements by their frequencies, which can help reduce the time complexity to **log** scale. Following illustrate solutions based both on a min heap and a max heap.
##### Min Heap
We again record frequency for each element first. Then, we construct a min heap which **maintains only the top-k most frequent elements**, because the least frequent element will be popped if the number of elements in the heap is greater than `k`. Finally, the top-k frequent elements in the min heap can be returned.
```python
def topKFrequent(nums: List[int], k: int) -> List[int]:
    num2freq = defaultdict(int)
    for num in nums:
        num2freq[num] += 1

    # Construct a min heap
    min_heap = []
    for num, freq in num2freq.items():
        heapq.heappush(min_heap, (freq, num))
        if len(min_heap) > k:
            heapq.heappop()

    topk = []
    for _ in range(k):
        topk.append(heapq.heappop(min_heap)[1])

    return topk
```
* Time complexity: $O(n\ log\ k)$
* Space complexity: $O(n)$
	* A min heap takes $O(k)$, but the worst case is still $O(n)$.
##### Max Heap
```python
```
* Time complexity: $O(k\ log\ n)$
* Space complexity: $O(n)$
#### 3. Bucket Sort
To further reduce time complexity induced by iterative **heapify** operations in idea 2, we can resort to **bucket sort**. The concept is summarized as follows,
1. Map each number to its frequency with a hash map.
2. Bin each number by its frequency (*i.e.,* buckets are indexed with possible frequencies).
	* It's easy to observe that the possible frequencies are in the range of `(0, len(nums)]`, in which `0` is a dummy.
3. Record the top-k frequent elements backward, because the more frequent the element, the larger the index is.
```python
def topKFrequent(nums: List[int], k: int) -> List[int]:
    num2freq = defaultdict(int)
    for num in nums:
        num2freq[num] += 1

    # Bin each number by its frequency
    buckets = [[] for _ in range(len(nums)+1)]
    for num, freq in num2freq.items():
        buckets[freq].append(num)
    
    topk = []
    for i in range(len(buckets)-1, -1, -1):
        for num in buckets[i]:
            topk.append(num)
            if len(topk) == k:
                return topk
```
* Time complexity: $O(n)$
	* Record number frequencies takes $O(n)$.
	* Bin (bucket) elements takes $O(n)$.
	* Taking the top-k most frequent elements takes $O(n)$.
* Space complexity: $O(n)$
	* A hash map recording the frequency of each unique number takes $O(n)$.
	* A list acting as buckets takes $O(n)$.