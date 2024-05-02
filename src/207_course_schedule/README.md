# [207. Course Schedule](https://leetcode.com/problems/course-schedule/)
> Determine if a student can finish all courses given the prerequisite course schedule. For each course pair `prerequisites[i] = [ai, bi]`,  one must take `bi` before `ai`.
### Input
* `numCourses`: Number of courses to take.
* `prerequisites`: A list of prerequisite course schedules.
### Output
`True` if all courses can be finished else `False`.
### Example
```
Input: numCourses = 2, prerequisites = [[1, 0]]
Output: true
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0. So it is possible.
```
### Idea
#### 1. BFS - Kahn's Algorithm
Find a possible topological sorting of the course graph. The core concept is summarized as follows,
1. First, record all sources which have no incoming nodes (*i.e.,* `in_deg == 0`).
2. For each course with `in_deg == 0`, safely remove it (think of it as a finished course) and decrease the number of prerequisites of all outgoing nodes (*i.e.,* `in_degs[nc_i] -= 1`).
3. Add any outgoing node with `in_deg == 0` to **about-to-finish** queue `q` .
4. Go back to 2. until `q` is empty.
If there's any course left unfinished (*i.e.,* `numCourses != 0`), the course schedule is failed.

Simply speaking, the process simulates **course registration** and **taking the course**. If a course has no prerequisites or all its prerequisites are finished, this course can be registered (*i.e.,* pushed into `good_to_take`).
```python
def canFinish(numCourses: int, prerequisites: List[List[int]]) -> bool:
    """BFS - Kahn's algorithm."""
    n_prevs = [0] * numCourses  # In-degree
    cur2next = defaultdict(list)
    for c_i, pc_i in prerequisites:
        # For each course and prev course pair
        cur2next[pc_i].append(c_i)
        n_prevs[c_i] += 1

    # Record all sources, having no prerequisites at start
    # Hence, these courses are good to take
    good_to_take = deque()
    for c_i, in_deg in enumerate(n_prevs):
        if in_deg == 0:
            good_to_take.append(c_i)

    while len(good_to_take) > 0:
        # Mark the course having been taken as finished
        c_i = good_to_take.popleft()
        numCourses -= 1

        for nc_i in cur2next[c_i]:
            # For each neighboring (next) course, the number of
            # prerequisites decreases by 1
            n_prevs[nc_i] -= 1
            if n_prevs[nc_i] == 0:
                # Prerequisites are all finishsed, so the course is
                # good to take now 
                good_to_take.append(nc_i)

    return numCourses == 0
```
* Time complexity: $O(|V|+|E)$
	* Each vertex is visited once, and all edges of each vertex are checked.
* Space complexity: $O(|V|+|E|)$
	* The adjacency list takes $O(|V|+|E|)$.
	* The in-degree tracker takes $O(|V|)$.
##### Comment
* Think how to implement the same algorithm from the perspective of **sinks**, instead of sources.
#### 2. DFS
Unlike BFS, this process can be thought of as a teacher inspecting whether the course map has any problem **path by path**. During course path inspection, if a course is being inspected, it's marked with temporary state `-1`. If the most advanced course is reached, safely mark it as finished and continue to check other course path.<br>
If a teacher again meets a course being inspected, the course path has a cycle. Hence, the course path is invalid.
```python
def canFinish(numCourses: int, prerequisites: List[List[int]]) -> bool:
    """DFS."""
    def can_take(c_i: int) -> bool:
        """Return if a course can be taken along a course path."""
        if course_status[c_i] in [-1, 1]:
            return course_status[c_i] == 1

        # Mark the current course as being inspected on the current
        # course path
        course_status[c_i] = -1
        for nc_i in cur2next[c_i]:
            if not can_take(nc_i):
                # If the following course path is invalid, the entire
                # course path is invalid too
                return False
        
        course_status[c_i] = 1
        return True

    course_status = [0] * numCourses
    cur2next = defaultdict(list)
    for c_i, pc_i in prerequisites:
        # For each course and prev course pair
        cur2next[pc_i].append(c_i)

    for c_i in range(numCourses):
        if course_status[c_i] == 0 and not can_take(c_i):
            return False

    return True
```
* Time complexity: $O(|V|+|E)$
	* Each vertex is visited once, and all edges of each vertex are checked.
* Space complexity: $O(|V|+|E|)$
	* The adjacency list takes $O(|V|+|E|)$.
	* The course status tracker takes $O(|V|)$.