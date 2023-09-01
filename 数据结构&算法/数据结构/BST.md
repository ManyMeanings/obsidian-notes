二叉搜索树（Binary Search Tree）
### 特性

1、对于 BST 的每一个节点 `node`，左子树节点的值都比 `node` 的值要小，右子树节点的值都比 `node` 的值大。

2、对于 BST 的每一个节点 `node`，它的左侧子树和右侧子树都是 BST。

**3、BST 的中序遍历结果是有序的（升序）；先遍历右子树的中序遍历是降序的**。

可以通过在二叉树节点中维护额外信息，把搜索时间复杂度降到对数级别

```java
lass TreeNode {
    int val;
    // 以该节点为根的树的节点总数
    int size;
    TreeNode left;
    TreeNode right;
}
```