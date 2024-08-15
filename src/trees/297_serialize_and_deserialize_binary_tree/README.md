# [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)
> Design an algorithm to serialize and deserialize a binary tree.
### Input
* `root` (serialize): Root of a binary tree.
* `data` (deserialize): Serialized code in **string**.
### Output
* Serialize: The serialized code in string form.
* Deserialize: Root of the original binary tree.
### Example
```
Input: root = [1,2,3,null,null,4,5]
Output: [1,2,3,null,null,4,5]
```
### Idea
To fully encode the binary tree, we must traverse it completely. Then, we must ensure the resulting binary tree can be **uniquely defined** by its serialized code. The key points are summarized as follows, 
1. If `null` leaves are discarded, all tree traversals can't guarantee an unique binary tree.
2. If `null` leaves are recorded, pre-, post-, and level-order traversals work fine.
#### 1. DFS - Preorder
```python
class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if root is None:
            return "#"
        
        code = str(root.val) + "," + self.serialize(root.left) + "," + self.serialize(root.right)
        return code
        

    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        def _dfs() -> Optional[TreeNode]:
            root_val = next(data)
            if root_val == "#":
                return None           

            root = TreeNode(root_val)
            root.left = _dfs()
            root.right = _dfs()

            return root

        data = iter(data.split(","))
        root = _dfs()

        return root
```
* Time complexity: $O(n)$
	* Visiting each node in the BT takes $O(n)$ for both serialization and deserialization.
* Space complexity: $O(h)$, where $h$ is the height of the BT.
	* The worst case happens when the BT is **skewed**, which has a call stack of depth $n$.
##### Comment
* It's like a Merkle tree!
* Postorder can also work.
```python
class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if root is None:
            return "#"
        
        code = self.serialize(root.left) + "," + self.serialize(root.right) + "," + str(root.val)
        return code


    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        def _dfs() -> Optional[TreeNode]:
            if data[self.i] == "#":
                self.i -= 1
                return None

            root = TreeNode(int(data[self.i]))
            self.i -= 1
            root.right = _dfs()
            root.left = _dfs()
            
            return root
           
        data = data.split(",")
        self.i = len(data) - 1
        root = _dfs()
        
        return root
```
* If the serialized code isn't necessary to be a string, we can just use `tuple` to serialize.
```python
class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if root is None:
            return None
        
        return (root.val, self.serialize(root.left), self.serialize(root.right))
        

    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        if data is None:
            return None

        root = TreeNode(data[0])
        root.left = self.deserialize(data[1])
        root.right = self.deserialize(data[2])

        return root
```
#### 2. BFS
Level-order traversal is also a feasible solution. For serialization, the BT is filled to **full** with special `null` nodes and encoded level by level. During deserialization, each visited node is viewed as the parent of the current subtree. If its child is not `None`, we build the child and push the child into the queue to handle later.
```python
class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if root is None:
            return ""
        
        code = []
        q = deque([root])
        while len(q) > 0:
            visited = q.popleft()
            if visited is None:
                code.append("#")
                continue
            
            code.append(str(visited.val))
            q.extend([visited.left, visited.right])

        return ",".join(code)


    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        if data == "":
            return None

        data = data.split(",")
        root = TreeNode(int(data[0]))
        q, i = deque([root]), 1
        while len(q) > 0:
            visited = q.popleft()

            if data[i] != "#":
                visited.left = TreeNode(int(data[i]))
                q.append(visited.left)
            i += 1

            if data[i] != "#":
                visited.right = TreeNode(int(data[i]))
                q.append(visited.right)
            i += 1

        return root
```
* Time complexity: $O(n)$
	* Visiting each node in the BT takes $O(n)$ for both serialization and deserialization.
* Space complexity: $O(n)$
	* A queue storing nodes to handle next takes $O(n)$. The worst case happens when the BT is **perfect**, in which the last level has $\frac{n+1}{2}$ nodes.
	* For serialize output, the worst case happens when the BT is **perfect**, which has a redundant level with all `None` (represented as `"#"` at the last level). But, it's still $O(n)$.
##### Comment
*  For the second point in the space complexity analysis, we can pop all `"#"` at last of serialization. But, additional index checking is required in deserialization.
```python
class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        if root is None:
            return ""
        
        code = []
        q = deque([root])
        while len(q) > 0:
            visited = q.popleft()
            if visited is None:
                code.append("#")
                continue
            
            code.append(str(visited.val))
            q.extend([visited.left, visited.right])

        while code[-1] == "#":
            code.pop()

        return ",".join(code)


    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        if data == "":
            return None

        data = data.split(",")
        root = TreeNode(int(data[0]))
        q, i = deque([root]), 1
        while len(q) > 0:
            visited = q.popleft()

            if i < len(data) and data[i] != "#":
                visited.left = TreeNode(int(data[i]))
                q.append(visited.left)
            i += 1

            if i < len(data) and data[i] != "#":
                visited.right = TreeNode(int(data[i]))
                q.append(visited.right)
            i += 1

        return root
```
### References
* [Which Tree traversal String is unique?](https://cs.stackexchange.com/questions/116655/which-tree-traversal-string-is-unique)
* [Why recording non-existent children in the pre-order traversal will differentiate different binary trees?](https://cs.stackexchange.com/questions/144593/why-recording-non-existent-children-in-the-pre-order-traversal-will-differentiat)
