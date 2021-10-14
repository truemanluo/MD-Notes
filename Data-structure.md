### :star:二叉树

#### 定义：

```c
typedef TreeNode {
	TreeNode *left;
	TreeNode *right;
	T value;
} TreeNode;
```

#### 二叉搜索树（Binary Search Tree）：

​	树为空，或者

- 左子树不为空时根节点的值大于左孩子的值
- 右子树不为空时根节点的值小于右孩子的值
- 且左子树和右子树均为二叉搜索树

1. 插入操作：

   - `root`为空则直接插入
   - 插入元素已经存在则`return false`
   - 否则从根节点遍历找到插入位置（插入点为`nullptr`）

   ```
      3					3
    /   \       --> 	  /   \
   1	  5				 1 	   5
   						  /
   						nullptr（插入4）
   ```

2. 删除操作：

   - 如果待删除节点`Node`不存在`return false`

   - 如果待删除节点无左右孩子，直接删除；
   - 如果待删除节点只有一个孩子，则用其孩子节点代替被删除节点
   - 如果待删除节点`Node`同时有左右孩子，则找到`Node`的后继节点`Node_next`，将其值拷贝给`Node`，然后删除`Node_next`（此时`Node_next`满足条件2或3）

   ```
      3					3		删除3				 4
    /   \       --> 	  /   \   	----->   		/   \
   1	  5				 1 	   5				   1     5
   						  /							/
   						4						   [4] 
   ```

最坏情况下，BST会退化成链表，为了解决该问题，引入平衡二叉树，常见的有**AVL-Tree和RB-Tree**.

#### AVL平衡二叉树

> AVL树在BST的基础上引入了保持平衡的约束条件。AVL树：
>
> - `root == nullptr`
> - 或者左右子树高度绝对值之差不超过1，且左右子树均为AVL树

1. 插入与删除操作：
   1. 按照BST插入与删除节点的方式操作
   2. 进行**re-balance**的操作

2. Re-balance：参考[wiki](https://zh.wikipedia.org/wiki/AVL%E6%A0%91)

   ```
   // []表示失去平衡的节点，()表示新的根节点 
   右旋：
             [3]					1		
            /   \       --> 	  /   \   	
          (1)	  5				 2 	   3				   
          / \                  / \   /  \			
         2   B                A   C B    5						   
   	 / \
   	A	C
   右旋：与右旋对称
   ```

   - LL：右旋
   - RR：左旋
   - LR：左旋 -> 右旋
   - RL：右旋 -> 左旋

#### 红黑树 

> AVL树由于其严格的平衡条件，在插入、删除过程中会有大量的re-balance操作。
>
> 为了降低插入和删除过程中调整的次数，引入了**红黑树**。
>
> :star2:要注意一点：就查询操作而言，AVL的平均查找速度会更快，**因为它是严格平衡的**。

红黑树是一种平衡条件相较AVL而言更宽松的二叉搜索树，它满足以下条件：

（1）节点为红或黑

（2）根节点为黑

（3）如果一个节点是红色的，那么其孩子节点均为黑色

（4）叶子节点为黑色（准确说是空节点为黑）

（5）**从一个节点出发到其子树中每个空节点的路径上，黑色节点的数目相同**

【引申】根据条件5，假设路径上黑色节点数目为`n`，则最短路径长度为`n`（只有黑色节点），最长路径长度为`2n`（根据条件4，只能交叉插入红色节点到路径中，最多插入`n`个），因此任意一条路径`p1`都不长于两倍的任意路径`p2`.

1. 插入：首先将待插入节点颜色设置为红色（不会违反条件5，一半的可能违反条件4，如果设置为黑色则必定会违反条件5），然后与BST类似，找到插入点插入节点，然后**re-balance**
2. 删除：
3. Re-balance：通过着色和旋转来进行re-balance操作：
   - 着色：
   - 旋转：
