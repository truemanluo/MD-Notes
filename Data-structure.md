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

1. 插入：首先将待插入节点颜色设置为红色（不会违反条件5，一半的可能违反条件3，如果设置为黑色则必定会违反条件5），然后与BST类似，找到插入点插入节点，然后**re-balance**
2. 删除：
3. Re-balance：通过着色和旋转来进行re-balance操作：
   - 着色：
   - 旋转：

### :star:高效数据结构

#### LRU

> 采用一个哈希表+双向链表的结构
>
> - 哈希表：`<key, Node*>`,值为一个指向`<key, value>`对的指针（迭代器）
>
> - 双向链表：每个`Node`是一个`<key, value>`对
>
>   > 为什么在哈希表中存了`key`，还需要在`Node`中存`key`?
>   >
>   > A：当链表长度超出容量极限时，需要移除尾节点，如果只存`value`的话无法用`O(1)`时间实现删除哈希表中的项，也就是说，需要一个从`value->key`的映射

![](https://gblobscdn.gitbook.com/assets%2F-MRP-_mTHVixnnXPCTr3%2F-Mf6gTon76CdutXuS6wv%2F-MfCIjMcztR2kp415FxC%2Fimage.png?alt=media&token=e4edb16f-cdf7-4895-898d-68107936366b)

- 实现要点：
  - `O(1)`复杂度删除链表节点，考虑双链表实现
  - 双向链表：用`dummyHead`和`dummyTail`来记录头和尾，简化在头部和尾部插入的边界判断
  - 要实现`O(1)`的`put`，考虑到容量超出限制的情况，每个`Node`是一个`<key, value>`对
  - 删除链表节点后，要及时释放内存，防止内存泄漏
- Code

```c++
class LRUCache {
private:
    struct Node {
        int __key;
        int __value;
        Node *pre;
        Node *next;
        Node(int key = 0, int value = 0):__key(key), __value(value), pre(nullptr), next(nullptr) {}
    };
    Node *head;
    Node *tail;
    unordered_map<int, Node*> dct;
    int __capacity;
public:
    LRUCache(int capacity):__capacity(capacity), head(new Node()), tail(new Node()) {
        head->next = tail;
        tail->pre = head;
    }
    
    int get(int key) {
        if (dct.find(key) == dct.end()) {
            return -1;
        }
        // remove key-val
        // add key-val to head
        transfertoFirst(dct[key]);
        return dct[key]->__value;
    }
    
    void put(int key, int value) {
        if (dct.find(key) != dct.end()) {
            dct[key]->__value = value;
            transfertoFirst(dct[key]);
        }
        else {
            // create a node
            Node *item = new Node(key, value);
            // insert to map
            dct.insert(make_pair(key, item));
            // addtoFirst
            addtoFirst(item);

            // check cache is full or not
            if (dct.size() > __capacity) {
                // remove key-node in dct
                dct.erase(tail->pre->__key);
                // remove last item in cache
                Node *oldnode = tail->pre;
                remove(tail->pre);
                delete oldnode;
            }
        }

    }
private:
    void addtoFirst(Node *cur) {
        head->next->pre = cur;
        cur->next = head->next;
        head->next = cur;
        cur->pre = head;
    }

    void remove(Node *cur) {
        cur->pre->next = cur->next;
        cur->next->pre = cur->pre;
    }

    void transfertoFirst(Node *cur) {
        // Node *oldnode = cur;
        remove(cur);
        addtoFirst(cur);
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

#### 跳表

![](https://assets.leetcode.com/uploads/2019/09/27/1506_skiplist.gif)

- 实现要点
  - 插入时，用栈来记录每一层的插入点，然后自底向上插入（有50%的概率插入）
  - 删除时自顶向下
- Code

```cpp
class Skiplist {
public:
    Skiplist(): head_(make_shared<Node>()) {

    }
    /**
    **
    **    [dummy] -> node1 -> ... -> ... -> node5 -> nil
    **                  |                      |
    **    [dummy] -> node1 -> node2 -> ..-> node5 -> nil
    **
    **/
    
    bool search(int target) {
        shared_ptr<Node> cur = head_;
        while (cur) {
            while (cur->next_) {
                if (target > cur->next_->value_)
                    cur = cur->next_;
                else if (target == cur->next_->value_) {
                    return true;
                }
                else {
                    break;
                }
            }
            // if (cur->next_ && target == cur->next_->value_) return true;
            cur = cur->down_;
        }
        return false;
    }
    
    void add(int num) {
        // 跳表是基于概率的一种数据结构，自底向上插入节点的概率依次减半
        stack<shared_ptr<Node>> positions; // 存储每一层待插入节点的集合
        for (auto cur = head_; cur; cur = cur->down_) {
            while (cur->next_ && cur->next_->value_ < num) {
                cur = cur->next_;
            } // 找到待插入的位置
            positions.push(cur);
        }

        // 自底向上进行插入
        bool insert = true; // 是否进行插入，初始化为true，因为level0是必然会插入
        shared_ptr<Node> down;

        while (!positions.empty() && insert) { // insert = true时才插入，否则直接结束
            auto node = positions.top(); positions.pop();
            shared_ptr<Node> n = make_shared<Node>(num, node->next_, down); // make_shared创建一个智能指针
            node->next_ = n;
            down = n;
            insert = rand() & 1;
        }

        // 考虑需要在最顶层插入的情况
        if (insert) {
            head_ = make_shared<Node>(-1, nullptr, head_);
        }


    }
    
    bool erase(int num) {
        // 每一层可能有节点的copy，因此要将每一层的删除干净
        bool found = false;
        for (auto cur = head_; cur; cur = cur->down_) {
            while (cur->next_ && cur->next_->value_ < num) {
                cur = cur->next_;
            }
            if (cur->next_ && num == cur->next_->value_) {
                cur->next_ = cur->next_->next_;
                found = true; // 跳表中至少存在一个num节点时返回true
            }
        }
        return found;
    }

private:
    struct Node {
        Node(int val = -1, shared_ptr<Node> next = nullptr, shared_ptr<Node> down = nullptr): value_(val), next_(next), down_(down) {}
        shared_ptr<Node> next_; // 智能指针防止内存泄露
        shared_ptr<Node> down_;
        int value_;
    };

    shared_ptr<Node> head_;
};

/**
 * Your Skiplist object will be instantiated and called as such:
 * Skiplist* obj = new Skiplist();
 * bool param_1 = obj->search(target);
 * obj->add(num);
 * bool param_3 = obj->erase(num);
 */
```

#### 字典（Trie）树

- 实现要点：

  - 递归定义`Node`结构：

    ```cpp
    	struct TrieNode {
            TrieNode():endwithChar(false), children(vector<TrieNode*>(26, nullptr)) {
    
            }
            ~TrieNode() {
                for (TrieNode* node : children) {
                    if (node) {
                        delete node;
                    }
                }
            }
            bool endwithChar;
            vector<TrieNode*> children;
        };
    ```

  - 防止内存泄漏

- Code 

```cpp
class Trie {
public:
    Trie():_root(new TrieNode()) {

    }
    ~Trie() {
        delete _root;
    }
    
    void insert(string word) {
        TrieNode* p = _root;
        for (auto ch : word) {
            if (!p->children[ch-'a']) {
                p->children[ch-'a'] = new TrieNode();
            }
            // move down
            p = p->children[ch-'a'];
        }
        p->endwithChar = true;
    }
    
    bool search(string word) {
        TrieNode* p = searchHelper(word);
        return p && p->endwithChar;
    }
    
    bool startsWith(string prefix) {
        TrieNode* p = searchHelper(prefix);
        return p != nullptr;
    }

private:
    struct TrieNode {
        TrieNode():endwithChar(false), children(vector<TrieNode*>(26, nullptr)) {

        }
        ~TrieNode() {
            for (TrieNode* node : children) {
                if (node) {
                    delete node;
                }
            }
        }
        bool endwithChar;
        vector<TrieNode*> children;
    };

    TrieNode* searchHelper(string &word) {
        TrieNode* p = _root;
        for (auto ch : word) {
            if (!p->children[ch-'a']) {
                return nullptr;
            }
            p = p->children[ch-'a'];
        }
        return p;
    }
    TrieNode* _root;
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```

#### 单调栈

> 栈是单调的，用于解决"下一个大/小于"的问题

