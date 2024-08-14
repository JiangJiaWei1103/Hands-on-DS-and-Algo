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
* Think about why only inorder can't work?
#### 2. BFS

