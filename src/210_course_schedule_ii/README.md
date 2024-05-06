# [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)
> Return the ordering of courses you should take to **finish all of them** given the prerequisite course schedule. For each course pair `prerequisites[i] = [ai, bi]`, one must take `bi` before `ai`.
### Input
- `numCourses`: Number of courses to take.
- `prerequisites`: A list of prerequisite course schedules.
### Output
A list containing any valid ordering of courses if possible else `[]`.
### Example
```
Input: numCourses = 4, prerequisites = [[1, 0], [2, 0], [3, 1], [3, 2]]
Output: [0, 2, 1, 3]
Explanation: There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0.
So one correct course order is [0, 1, 2, 3]. Another correct ordering is [0, 2, 1, 3].
```
### Idea
#### 1. BFS - Kahn's Algorithm
Find a possible topological sorting of the course graph. To traverse the course graph in a valid way, following information is needed,
1. The number of prerequisites **left** to take.
2. The relationship of the current course (*i.e.,* prerequisite) and the next course (*i.e.,* advanced).
The process is summarized as follows,
1. Start from those without prerequisites (`n_prev == 0`) in the beginning, just push them to`good_to_take`.
2. Add one valid course to the course path as if the student just finishes it.
3. For each taken course, the number of prerequisites of its next course decreases by 1.
4. If all courses are successfully taken, return the valid course path.
```python
def findOrder(numCourses: int, prerequisites: List[List[int]]) -> List[int]:
    n_prevs = [0] * numCourses
    cur2next = defaultdict(list)
    for c_i, pc_i in prerequisites:
        cur2next[pc_i].append(c_i)
        n_prevs[c_i] += 1

    good_to_take = deque()
    for c_i, n_prev in enumerate(n_prevs):
        if n_prev == 0:
            good_to_take.append(c_i)
    
    course_path = []
    while len(good_to_take) > 0:
        # Take the current course
        c_i = good_to_take.popleft()
        course_path.append(c_i)

        for nc_i in cur2next[c_i]:
            n_prevs[nc_i] -= 1
            if n_prevs[nc_i] == 0:
                good_to_take.append(nc_i)
    
    if len(course_path) == numCourses:
        return course_path
    else:
        return []
```
* Time complexity: $O(|V| + |E|)$
	* Each vertex is visited once, and all edges of each vertex are checked.
* Space complexity: $O(|V| + |E|)$
	* The adjacency list takes $O(|V| + |E|)$.
	* The in-degree tracker takes $O(|V|)$.
	* The course path takes at most $O(|V|)$.
#### 2. DFS
Think of the process as a course path inspection, taken by a teacher checking if all course sub-paths are valid. From the perspective of course sub-paths, the process can be summarized as follows,
1. During inspection, each course being visited is marked with a temporary status `-1`.
2. Going deeper toward more advanced courses, the sub-path becomes invalid if there exists any cycle (*i.e.,* encountering a course with `-1`, being inspected).
3. If all courses on the following sub-path are valid, the one being inspected is marked with a valid status `1`, and the course is added to the final course path.
4. Switch to another sub-path if the current is valid.
5. If all course sub-paths are valid, reverse the valid course path and return it.
The final course path is reversed because the course map is inspected with advanced courses first.
```python
def findOrder(numCourses: int, prerequisites: List[List[int]]) -> List[int]:
    def can_take(c_i: int) -> bool:
        if course_status[c_i] in {-1, 1}:
            return course_status[c_i] == 1

        course_status[c_i] = -1
        for nc_i in cur2next[c_i]:
            if not can_take(nc_i):
                return False
        course_status[c_i] = 1
        course_path.append(c_i)
        
        return True
    
    course_status = [0] * numCourses
    cur2next = defaultdict(list)
    for c_i, pc_i in prerequisites:
        cur2next[pc_i].append(c_i)

    course_path = []
    for c_i, status in enumerate(course_status):
        if status == 0 and not can_take(c_i):
            return []

    # Because course path inspection is taken from the most advanced
    # course, the ordering of taken courses must be reversed
    course_path.reverse()

    return course_path


def findOrder(numCourses: int, prerequisites: List[List[int]]) -> List[int]:
    """Check the most basic courses first.
    
    In this solution, the adjacency list records each course pair
    pointing from the current course to the prerequisite. Hence, DFS
    inspects the most basic courses first. Then, topological sorting
    follows the correct direction.
    """
    def can_take(c_i: int) -> bool:
        if course_status[c_i] == -1:
            return False
        elif course_status[c_i] == 1:
            return True

        course_status[c_i] = -1
        for pc_i in cur2prev[c_i]:
            if not can_take(pc_i):
                # If the prerequisite can't be taken
                return False
        course_status[c_i] = 1
        course_path.append(c_i)
        
        return True
    
    course_status = [0] * numCourses
    cur2prev = defaultdict(list)
    for c_i, pc_i in prerequisites:
        cur2prev[c_i].append(pc_i)

    course_path = []
    for c_i, status in enumerate(course_status):
        if status == 0 and not can_take(c_i):
            return []

    return course_path
```
* Time complexity: $O(|V| + |E|)$
	* Each vertex is visited once, and all edges of each vertex are checked.
* Space complexity: $O(|V| + |E|)$
	* The adjacency list takes $O(|V| + |E|)$.
	* The course status tracker takes $O(|V|)$.
	* The course path takes at most $O(|V|)$.