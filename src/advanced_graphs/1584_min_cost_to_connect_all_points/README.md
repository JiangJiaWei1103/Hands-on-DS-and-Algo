# [1584. Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/)
> Derive the minimum cost to connect all points in a 2D plane in terms of the manhattan distance.
### Input
* `points`: A list of points in a 2D plane.
	* `1 <= points.length <= 1000`
	* `-10^6 <= xi, yi <= 10^6`
	* All pairs `(xi, yi)` are distinct.
### Output
The min cost to connect all points.
### Example
```
Input: points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
Output: 20
```
### Idea
The core idea is to find a possible minimum spanning tree within this **complete graph**, in which each node is supposed to connect **all the others**.
#### 1. Prim's Algorithm
The high-level process can be summarized as follows,
1. Start spanning from a randomly picked point.
2. Add the point (*i.e.,* fringe, adjacent but hasn't been visited) with the minimum cost to the BST.
3. Expand the fringe vertices.
4. Iterate 2 and 3 until the BST is completed.

For implementation, we first consider using a min heap to keep track of the fringe vertex with the minimum cost. Also, we need to avoid duplicated visitings on those having been visited (*i.e.,* already in the BST).
```python
def minCostConnectPoints(points: List[List[int]]) -> int:
    # Create a cost-aware adjacency list of the complete graph
    n = len(points)
    adj = [[] for _ in range(n)]
    for i in range(n):
        for j in range(n):
            if i == j:
                continue
                
            x1, y1 = points[i]
            x2, y2 = points[j]
            cost = abs(x1 - x2) + abs(y1 - y2)
            adj[i].append((cost, j))

    # Run Prim's algo to find the BST
    bst = set()
    tot_cost = 0
    heap = [(0, 0)]
    while len(bst) < n:
        cur_cost, u = heapq.heappop(heap)
        if u in bst:
            continue

        bst.add(u)
        tot_cost += cur_cost 
        for nei_cost, nei in adj[u]:
            if nei in bst:
                continue
                
            heapq.heappush(heap, (nei_cost, nei))
    
    return tot_cost
```
* Time complexity: $O(n^2\ log\ n^2) \approx O(n^2\ log\ n)$
	* Pre-computing pair-wise costs takes $O(n^2)$.
	* Running the Prim's algorithm with a min heap requires $n^2$ heap operations, each of which takes $O(log\ n^2)$ to complete.
* Space complexity: $O(n^2)$
	* Both the adjacency list of the complete graph and the min heap take $O(n^2)$.
	* A set storing the visiting status (*i.e.,* the BST snapshot) takes $O(n)$.
##### Comment
* Instead of pre-computation, we can also calculate the cost on the fly as we need. Note that this makes no difference in terms of time and space complexities.
```python
def minCostConnectPoints(points: List[List[int]]) -> int:
    def _cal_cost(u: int, v: int) -> int:
        x1, y1 = points[u]
        x2, y2 = points[v]
        cost = abs(x1 - x2) + abs(y1 - y2)

        return cost

    n = len(points)
    bst = set()
    tot_cost = 0
    heap = [(0, 0)]
    while len(bst) < n:
        cur_cost, u = heapq.heappop(heap)
        if u in bst:
            continue

        bst.add(u)
        tot_cost += cur_cost 
        for nei in range(n):
            if nei not in bst:
                heapq.heappush(heap, (_cal_cost(u, v=nei), nei))
    
    return tot_cost 
```
* Instead of using a min heap, we can use an 1D array to track the cost to reach **fringe** vertices from the current BST snapshot, then TC improves to $O(n^2)$ and SC to $O(n)$.
```python
def minCostConnectPoints(points: List[List[int]]) -> int:
    def _cal_cost(u: int, v: int) -> int:
        x1, y1 = points[u]
        x2, y2 = points[v]
        cost = abs(x1 - x2) + abs(y1 - y2)

        return cost
    
    def _get_min(costs: List[int]) -> Tuple[int, int]:
        min_cost = float("inf")
        argmin = 0
        for i, cost in enumerate(costs):
            if cost < min_cost:
                min_cost = cost
                argmin = i
        
        return min_cost, argmin

    n = len(points)
    fringe_costs = [float("inf")]
    for v in range(1, n):
        fringe_costs.append(_cal_cost(u=0, v=v))

    bst = set([0])
    tot_cost = 0
    while len(bst) < n:    
        cur_cost, u = _get_min(fringe_costs)
        tot_cost += cur_cost
        bst.add(u)
        fringe_costs[u] = float("inf")
        for v in range(n):
            if v in bst:  # or fringe_costs[v] == float("inf")
                continue

            fringe_costs[v] = min(fringe_costs[v], _cal_cost(u, v))

    return tot_cost
```