### Leetcode fast break

【1】两数之和

> - 法1：用map存储满足条件的差值
>   - `O(n)`空间+`O(n)`时间
> - 法2：**排序**之后用**双指针**
>   - `O(1)`空间+`O(nlogn)`时间

【2】两数相加（链表）

> 模拟法
>
> **NOTE：**
>
> - 链表长度不一时可以类似统一处理：`num1 = l1 ? l1->val : 0;`
> - 不要忘记最后的进位
> - `l = l->next`之前先判断`l == nulptr`?

【3】:star: 无重复字符的最长子串（字符串+滑动窗口）

> 输入: s = "abcabcbb"
>
> - 法1：维护一个hashmap，存储每个字符最后出现的位置，以此得到以**当前字符结尾的不重复字符串**。
>
>   滑动窗口维护的是以**当前字符结尾的不重复字符串**。
>
>   要注意的点：
>
>   - 除Hashmap外还需要维护一个到当前字符前不重复字符的起点`start`,按照`start = max(start, dct[s[i]]+1)`。
>
>     防止这种情况：`"abba"`, `start = 2`，`a`最后一次出现的位置为`0`，因此需要取`max`才能满足条件

【4】:star:寻找两个正序数组的中位数

> - 法1：归并
>
> - [ ] 法2：二分

【5】:star:最长回文子串

> - 法1：扩展中心法
>   - 以`(i, i)`和`(i, i + 1)`进行中心拓展，直到不满足条件
> - 法2：DP
>   - $$f(i, j)$$表示`[i, j]`之间的数是回文数， 则$$f(i, j) = (s[i] == s[j] \& f(i+1, j-1))$$
>
> Code：
>
> ```c++
> string longestPalindrome(string s) {
>     string ans;
>     int start = 0, maxlen = 1;
>     int n = s.size();
>     vector<vector<bool>> dp(n, vector<bool>(n));
>     for (int i = 0; i < n; ++i) dp[i][i] = true;
>     for (int i = 1; i < n; ++i) {
>         for (int j = 0; j < i; ++j) {
>             if (s[i] == s[j] && (i - j < 3 || dp[i-1][j+1])) { // 此处i - j < 3其实导致不用初始化dp数组
>                 dp[i][j] = true;
>                 if (i - j + 1 > maxlen) {
>                     maxlen = i-j+1;
>                     start = j;
>                 } 
>             }
>         }
>     }
>     return s.substr(start, maxlen);
> }
> ```

【6】

- [ ] Z 字形变换

【7】:star:整数反转

> - 关键在于溢出的处理：
>   - 提前判断待乘的数是否大于`INT_MAX/10`
>
> ```c++
> 	int str_to_int(string number, int sign) {
>         int ans = 0;
>         int boundry = (int)INT_MAX / 10;
>         for (int i = 0; i < number.size(); ++i) {
>             int cur = number[i] - '0';
>             if (ans > boundry || (ans == boundry && cur > 7)) {
>                 return sign == -1 ? INT_MIN : INT_MAX;
>             }
>             ans = ans*10 + cur;
>         }
>         return ans*sign;
>     }
> ```

- [x] 剑指 Offer 67. 把字符串转换成整数（同7）

【8】字符串转换整数 (atoi)（同7）

【9】回文数

> 核心：
>
> - 为了防止溢出，只反转数字的一半
> - 判断反转一半的条件：`x_reverted >= x`
> - 返回的条件：
>   - 长度为奇数时：`x == x_reverted/10`
>   - 长度为偶数时：`x == x_reverted`
>
> 例子：<img src="https://assets.leetcode-cn.com/solution-static/9/9_fig1.png" style="zoom: 50%;" />

【11】盛最多水的容器（双指针/贪心）

> - 贪心策略：初始化`i = 0, j = n-1`，随着`i, j`向中间移动，容器的宽度是减少的，因此只有**移动`num[i],num[j]`之间的较小者**才有可能增大容积（短板效应）
> - 双指针：指针由两侧向中间移动

【12】整数转罗马数字

> 关键：其实也是一种贪心策略，将对应的数字-罗马数字存起来，由高到低取（遍历是尽量取每个`item`，直到```num < item```）
>
> ```c++
> const pair<int, string> valueSymbols[] = {
>     {1000, "M"},
>     {900,  "CM"},
>     {500,  "D"},
>     {400,  "CD"},
>     {100,  "C"},
>     {90,   "XC"},
>     {50,   "L"},
>     {40,   "XL"},
>     {10,   "X"},
>     {9,    "IX"},
>     {5,    "V"},
>     {4,    "IV"},
>     {1,    "I"},
> };
> ```

【13】罗马数字转整数

> 关键：只要左边对应的罗马数字小于其相邻右侧的罗马数字，则加上负的

【14】最长公共前缀

> - 法1：横向搜索
>
> <img src="https://assets.leetcode-cn.com/solution-static/14/14_fig1.png" style="zoom: 25%;" />
>
> - 法2：纵向搜索（从字符串的每一列开始遍历）
>
>   注意终止条件：`i == strs[j].size() || strs[j][i] != c`（到达当前字符串长度时或者不等于当前字符）
>
> - 法3：分治

【15】:star:三数之和

> - 法：转化成两数之和+双指针
>
> 当我们需要枚举数组中的两个元素时，如果我们发现随着第一个元素的递增，第二个元素是递减的，那么就可以使用双指针的方法，将枚举的时间复杂度从 O(N^2)减少至 O(N).

【16】:star:最接近的三数之和

> - 法：与14类似，不同的是**直接算三数之和然后比较绝对值**

【17】电话号码的字母组合

> - 法：递归生成combination
>
> Notes: 递归的参数是index，每个对应的index都要进行`[0, n-1]`的遍历

【19】删除链表的倒数第 N 个结点

【20】有效的括号

【21】合并两个有序链表

【24】[两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

> - 法1：递归
> - 法2：迭代

![image-20210728102622389](https://i.loli.net/2021/07/28/WpPEL79Hsc6eg12.png)

【25】:star::star::star:K个一组翻转链表

![image-20210728105952626](https://i.loli.net/2021/07/28/hHKc2yIt7jaRD6V.png)

【26】[删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

> 快慢指针

1. 环形链表 II

   <img src="https://gblobscdn.gitbook.com/assets%2F-MRP-_mTHVixnnXPCTr3%2F-Mf6gTon76CdutXuS6wv%2F-MfBqsyg_7O_lNU8kAh4%2Fimage.png?alt=media&amp;token=82754ce5-5e07-4bd8-8bf8-35ba88e43890" style="zoom:67%;" />

2. :star::star::star:LRU

   > 设计1：双向链表中每一个entry是key-value的pair，目的是删除LRU元素时能直接访问到key，实现O(1)时间复杂度的删除
   >
   > - 哈希表用于查询
   > - 双向链表用于更新元素的优先级
   > - 链表头存储最近访问的，链表尾存放LRU

![](https://gblobscdn.gitbook.com/assets%2F-MRP-_mTHVixnnXPCTr3%2F-Mf6gTon76CdutXuS6wv%2F-MfCIjMcztR2kp415FxC%2Fimage.png?alt=media&token=e4edb16f-cdf7-4895-898d-68107936366b)

39. 组合总和

    > DFS

70. 爬楼梯

    > 法1：递归（TLE）
    >
    > 法2：DP

215. :star::star::star:数组中第K大的元素

     > 法1：快速排序
     >
     > - 快排分割：
     >
     >   ```c++
     >   int partition(vector<int>& nums, int start, int end) {
     >           int i = start, j = end;
     >           int pivot = start;
     >           while (i < j) {
     >               while (i < j && nums[j] <= nums[pivot]) {
     >                   --j;
     >               }
     >               while (i < j && nums[i] >= nums[pivot]) {
     >                   ++i;
     >               }
     >               if (i >= j) break;
     >               swap(nums[i], nums[j]);
     >           }
     >           swap(nums[j], nums[pivot]);
     >           return j;
     >       }
     >   ```
     >
     > - 随机打乱：
     >
     >   ```c++
     >   inline int randomPartition(vector<int>& a, int l, int r) {
     >           int i = rand() % (r - l + 1) + l;
     >           swap(a[i], a[r]);
     >           return partition(a, l, r);
     >       }
     >   ```
     >
     > 法2：堆
     >
     > - C++ API：建立一个小根堆（大小为k），初始化为前k个元素。从k+1个元素开始，如果当前元素小于堆顶元素，则用当前元素替换堆顶元素。**最后得到的堆堆顶即为第k大的元素**
     >
     >   Notes：C++中小根堆建立方法
     >
     >   ```std::priority_queue<int, std::vector<int>, std::greater<int>> q;```
     >
     > - 自建堆
     >
     >   Notes: 堆的下沉操作（**此操作是建立在除了当前根节点以外，其余均满足堆的条件**）
     >
     >   - 迭代法
     >
     >   ```c++
     >   void sink(vector<int> &nums, int start, int length) {
     >           int tmp = nums[start];
     >           for (int i = 2*start+1; i < length; i = i*2+1) {
     >               if (i < length-1 && nums[i+1] > nums[i]) {
     >                   ++i;
     >               }
     >               if (nums[i] > tmp) {
     >                   nums[start] = nums[i];
     >                   start = i; 
     >               }
     >               else {
     >                   break;
     >               }
     >           }
     >           nums[start] = tmp;
     >       }
     >   ```
     >
     >   - 递归

236. :star::star::star:二叉树的最近公共祖先

     > youtuber:[Tushar Roy - Coding Made Simple](https://www.youtube.com/channel/UCZLJf_R2sWyUtXSKiKlyvAw)

     ![](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)
     
     > 法1：分别存储`root`到`p`和`q`的路径，找出他们的分叉点
     >
     > 法2：存储树中每个节点的父节点，然后从`p`往前遍历，用`set`记录遍历过的节点，最后从`q`往前遍历，遇到访问过的节点则返回
     >
     > 法3：递归
     >
     > > 前提：`p`,`q`均在树中
     >
     > ```c++
     > TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
     >         if (!root) return nullptr;
     >         if (root == p || root == q) return root;
     >         TreeNode* left = lowestCommonAncestor(root->left, p, q);
     >         TreeNode* right = lowestCommonAncestor(root->right, p, q);
     >         if (!left && !right) return nullptr;
     >         if (left && right) return root;
     >         return left ? left : right;
     >     }
     > ```

43. :star::star::star:二叉搜索树的最近公共祖先

【144】二叉树的前序遍历（迭代）

> 先将`root`加入栈中

```c++
 vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;

        stack<TreeNode*> stk;
        stk.push(root);
        while (!stk.empty()) {
            TreeNode *top = stk.top();
            stk.pop();
            ans.push_back(top->val);
            if (top->right) {
                stk.push(top->right);
            }
            if (top->left) {
                stk.push(top->left);
            }
        }
        return ans;
    }
```

【94】[二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)（迭代）

> 初始化`cur`节点，中止条件为：右子树没有节点且栈为空

```c++
vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;

        stack<TreeNode*> stk;
        TreeNode *cur = root;
        while (cur != nullptr || !stk.empty()) {
            while (cur) {
                stk.push(cur);
                cur = cur->left;
            }
            cur = stk.top();
            stk.pop();
            ans.push_back(cur->val); // 根节点入栈
            cur = cur->right; // 去遍历右子树
        }

        return ans;

    }
```



【225】用队列实现栈

> 法：队列中队头维护栈顶，队尾维护栈底
>
> - `push`如何实现？
>
>   每次入栈之前先计算队列长度-->入队当前元素`x`-->将`x`之前的元素依次出队并从队尾入队
>
> ```c++
> /** Push element x onto stack. */
>     void push(int x) {
>         int sz = que.size();
>         que.push(x);
>         for (int i = 0; i < sz; ++i) {
>             que.push(que.front());
>             que.pop();
>         }
>     }
> ```
>
> 

【435】 无重叠区间

> - 法1：动态规划，`f[i]`表示以`i`区间为最后一个区间，可以选出的区间数量的最大值
>
>   转移方程：`f[i] = max({f[j]}) + 1`
>
> - 法2：贪心
>
>   核心思想是：结束小的优先安排，然后选下一个不重合区间，变成一个子问题
>
>   > 用lamda将区间排序（结束小的在前）
>
> 

