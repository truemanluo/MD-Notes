### 常见算法

【2022.11.22 update】

![image-20211122224153510](https://i.loli.net/2021/11/22/FLAWDT934pfrGsC.png)



#### :star:动态规划

- 带增加删除修改的**DP**

  - [1186. 删除一次得到子数组最大和](https://leetcode-cn.com/problems/maximum-subarray-sum-with-one-deletion/)

    用两个`dp`数组来解决：
  
    - `dp0`存储最大连续子数组的和，`dp0[i]`表示以`i`结尾的最大连续数组的和
    - `dp1`存储删除一个元素后的最大子数组和，`dp1[i]`表示以`i`结尾的删除一个元素后的最大连续子数组的和
  
    则`dp0[i]`可以表示为：
  
  $$
  dp0[i] = (dp0[i-1]+nums[i], nums[i])
  $$
  ​	`dp1[i]`表示为：
  $$
  dp1[i] = max(dp1[i-1]+nums[i], dp0[i-1])
  $$
  ​	那么所求的最大和可以表示为：
  $$
  dp[i] = max(dp0[i], dp1[i])
  $$
  
- 区间**DP**

  - [375. 猜数字大小 II](https://leetcode-cn.com/problems/guess-number-higher-or-lower-ii/)

  - [877. 石子游戏](https://leetcode-cn.com/problems/stone-game/)

#### :star:前缀和

- [560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

  > 给你一个整数数组 nums 和一个整数 k ，请你统计并返回该数组中和为 k 的连续子数组的个数。
  >
  > 示例 1：
  >
  > 输入：nums = [1,1,1], k = 2
  > 输出：2
  >
  > 示例 2：
  >
  > 输入：nums = [1,2,3], k = 3
  > 输出：2

  - `O(n^2)`解法：

    枚举`[i, j]`的子数组，前缀和以`O(1)`复杂度得到`[i, j]`之间的和

    ```cpp
    	// TLE
    	int subarraySum(vector<int>& nums, int k) {
            int n = nums.size();
            if (n == 0) return k == 0? 1 : 0;
            int cnt = 0;
            for (int i = 0; i < n; ++i) {
                int sum = 0;
                for (int j = i; j < n; ++j) {
                    sum += nums[j];
                    if (sum == k) {
                        ++cnt;
                    }
                }
            }
            return cnt;
        }
    ```

  - :star:`O(n)`解法：前缀和 + 哈希表

    令$prefix[i]$表示$[0, i]$之间元素的和，因此$[j, i]$之间的元素和表示为：
    $$
    sum(j, i) = prefix[i] - prefix[j-1],
    $$
    题目要求得和为$k$的子序列，则有：
    $$
    k = prefix[i] - prefix[j-1],
    $$
    移项得
    $$
    prefix[j-1] = prefix[i] - k,
    $$
    因此可以**用一个哈希表记录$prefix[j-1]$​的出现次数`occurance`，每次遍历到`i`时更新：`ans += occurance`**

    > Why it works?
    >
    > - $prefix[j-1]$出现不止一次说明什么？
    >
    >   假设`prefix[j-1] = sum, dct[prefix[j-1]] = 2`，原数组为`nums = [x, x...x] + [y, y, y...y] + [z, z, z...z]` ，`x`和`z`数组前缀和均为`sum`，**则表示`y`数组的和为`0`，则加上`y`数组对和没有影响，但是却也是满足条件的子数组**

    以`[1, -1, 1, 2, 1, -1, -2, 2]`，`k = 0`为例，前缀和为：

    `[1, 0, 1, 3, 4, 3, 1, 3]`，`i = 6`时，`dct[1-0] = 2`

- [1171. 从链表中删去总和值为零的连续节点](https://leetcode-cn.com/problems/remove-zero-sum-consecutive-nodes-from-linked-list/)

#### :star:BFS

1. 求解最短路问题：

   > 从一个状态到最终状态的**最短路径**

   - [397. 整数替换](https://leetcode-cn.com/problems/integer-replacement/)

#### :star:链表排序

1. 归并排序：

   Bottom-up解法分为三步：

   - 更新`step`从`1->2->4...sz/2`

   - 针对每一个`step`，执行**cut**操作，即：

     ```
     1->2->4->6->9->10
     --------cut-------
     		||step = 2
     		||
     1->2->null, return 4->6->...
     4->6->null, return 9->10->...
     9->10->null, return nullptr
     ```

   - 两两**merge**，并将合并的链表接到`tail`后

   代码如下：

   ```c++
   class Solution {
   public:
       ListNode* sortList(ListNode* head) {
           // merge sort
           if (!head) return nullptr;
           int cnt = 0;
           for (ListNode *cur = head; cur ; cur = cur->next) {
               ++cnt;
           }
           ListNode *dummy = new ListNode();
           dummy->next = head;
           for (int step = 1; step < cnt; step *= 2) {
               ListNode *tail = dummy, *cur = dummy->next;
               while (cur) {
                   // cut
                   ListNode *left = cur;
                   ListNode *right = cut(cur, step);
                   cur = cut(right, step);
                   tail->next = merge(left, right);
                   while (tail->next) {
                       tail = tail->next;
                   }
               }
           }
           return dummy->next;
       }
   private:
       ListNode* cut(ListNode *head, int n) {
           if (!head) {
               return nullptr;
           }
           while (--n > 0 && head) {
               head = head->next;
           }
           if (!head) {
               return nullptr;
           }
           ListNode *ans = head->next;
           head->next = nullptr;
           return ans;
       }
   
       ListNode* merge(ListNode *l1, ListNode *l2) {
           if (!l1) return l2;
           if (!l2) return l1;
           // 1->3      --
           // 2->4      --  1->2->3->4
           ListNode *dummy = new ListNode();
           ListNode *tail = dummy;
           while (l1 && l2) {
               if (l1->val < l2->val) {
                   tail->next = l1;
                   l1 = l1->next;
               }
               else {
                   tail->next = l2;
                   l2 = l2->next;
               }
               tail = tail->next;
           }
           tail->next = l2 ? l2 : l1;
           return dummy->next;
       }
   };
   ```

   #### :star:子序列相关问题

   - 【:warning:需要加深理解】[128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

     > 要求O(n)复杂度的话考虑采用额外的空间（哈希表、set或数组）

     1. 两次遍历：
        - 用`set`存储出现的数字
        - **从`lower bound`进入循环**，更新最大长度

     ```cpp
     int longestConsecutive(vector<int>& nums) {
             if (nums.empty()) return 0;
             int ans = 1;
             unordered_set<int> s(nums.begin(), nums.end());
             for (auto n : nums) {
                 if (s.count(n-1)) continue;
                 int len = 1;
                 while (s.count(++n)) {
                     ++len;
                 }
                 ans = max(ans, len);
             }
             return ans;
         }
     ```

     2. 一次遍历：

        - 用哈希表维护一个连续子序列的`lower bound`和`upper bound`

        - 当发现可形成连续序列时，更新`lower bound`和`upper bound`

        - **将当前元素插入到哈希表中**：这一步很重要，否则当出现重复元素时，左右边界可能会被重复更新

          ```
          [1:3] [2] [3:3] 4 [5:2] [6:2]
          1. 当前元素作为bridge：当前4将左右两段连接成一个连续序列[1,2,3,4,5,6]
          --> [1:6][2,3,4,5][6:6]
          2. 当前元素作为右边界：[1:3][2][3:3]4
          3. 当前元素作为左边界：4[5:2][6:2]
          ```

          以`[4,1,3,2,2]`为例，遍历到最后一个`2`时，`dct = {1:4, 3:2, 4:4}`，如果之前没有将`2`插入哈希表中，则会重复更新左边界，即：

          ```
          [1	2	3	4]
          		[3	4]
          ```

          

