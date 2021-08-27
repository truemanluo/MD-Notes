### 剑指Offer

【剑指 Offer 04】 二维数组中的查找

> 从右上或者左下开始进行查找

【剑指 Offer 09】用两个栈实现队列

> 法：
>
> - 一个栈`stk_in`只负责进
> - 一个栈`stk_out`只负责出
> - 只有`stk_out`为空时才需要将`stk_in`中的元素倒序放入其中

```c++
class CQueue {
public:
    CQueue() {

    }
  
    void appendTail(int value) {
        stk.push(value);
    }
    
    int deleteHead() {
        if (aux.empty()) {
            if (stk.empty()) return -1;
            while (!stk.empty()) {
                aux.push(stk.top());
                stk.pop();
            }
        }
        int front = aux.top();
        aux.pop();
        return front; 
    }

private:
    stack<int> aux, stk;
};
```

【剑指 Offer 12】矩阵中的路径

> 典型的搜索问题。
>
> - 法：深度优先搜索
>
> Notes：（1）将二维数组中每个元素当成起点进行搜索
>
> ​			（2）标记访问过的数组

【剑指 Offer 13】 机器人的运动范围

> DFS+回溯

【剑指 Offer 07】重建二叉树

> - 法1：递归构建
>
> Notes：（1）处理带数组的递归时，一定要注意越界的情况（递归的边界条件）
>
> ```c++
> class Solution {
> public:
>     TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
>         if (preorder.size() != inorder.size() || preorder.empty()) return nullptr;
>         int n = preorder.size();
>         return rebuildTree(preorder, inorder, 0, n-1, 0, n-1);
>     }
> private:
>     TreeNode* rebuildTree(vector<int>& preorder, vector<int>& inorder, int p_start, int p_end, int i_start, int i_end) {
>         if (p_end < p_start || i_end < i_start) return nullptr; // 处理越界问题
>         // if (p_end == p_start && i_start == i_end) return new TreeNode(preorder[p_start]);
>         TreeNode *root = new TreeNode(preorder[p_start]);
>         int i = i_start;
>         for (; i <= i_end; ++i) {
>             if (inorder[i] == preorder[p_start]) break;
>         }
>         root->left = rebuildTree(preorder, inorder, p_start+1, p_start+i-i_start, i_start, i-1);
>         root->right = rebuildTree(preorder, inorder, p_start+i-i_start+1, p_end, i+1, i_end);
>         return root;
>     }
> };
> ```

【剑指 Offer 14- I】 剪绳子

> 法1：动态规划
>
> `dp[i]`表示长度为`i`的绳子可剪出的最大乘积；
>
> - 转移方程：$dp[i] = max(dp[i], max(j*(i-j), j*dp[i-j]))$​
>
> 有点类似于背包问题：
>
> - 第一层循环表示总长度，第二层循环表示从2开始剪，第二层循环中的到的最大值即为`dp[i]`
>
> ```c++
> 	int cuttingRope(int n) {
>         vector<int> dp(n+1, 1);
>         for (int i = 3; i <= n; ++i) { // 绳子的长度
>             for (int j = 2; j < i; ++j) { // 第一次剪的长度
>                 dp[i] = max(dp[i], max(j*(i-j), j*dp[i-j]));
>             }
>         }
>         return dp[n];
>     }
> ```
>
> 法2：[数学](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/solution/mian-shi-ti-14-i-jian-sheng-zi-tan-xin-si-xiang-by/)
>
> - 算数几何不等式->求极值

【剑指 Offer 14- II】剪绳子 II

> 法：主体同上+大数取余数
>
> - [循环求余](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/solution/mian-shi-ti-14-ii-jian-sheng-zi-iitan-xin-er-fen-f/)
> - 快速幂（不会）

【[剑指 Offer 34. 二叉树中和为某一值的路径](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)】

> 法：DFS
>
> - 注意返回条件：如果`root == nullptr`就应该直接返回（意味着没有提前返回结果，当前结果不满足条件）
>
> - 先加入，再判断
>
>   ```c++
>   void dfs(TreeNode* root, vector<vector<int>> &ans, int target, vector<int> &tmp) {
>       // 终止条件
>       if (!root) {
>           return;
>       }
>       tmp.push_back(root->val);
>       if (!root->left && !root->right && target == root->val) {
>           ans.push_back(tmp);
>       }
>       dfs(root->left, ans, target-root->val, tmp);
>       dfs(root->right, ans, target-root->val, tmp);
>       tmp.pop_back();
>   }
>   ```
>
>   

