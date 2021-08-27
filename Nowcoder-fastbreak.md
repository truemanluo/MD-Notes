### 牛客题解

【NC17】最长回文串

【NC32】求平方根

> 当`i == j`时，分两种情况：
>
> - `mid < x / mid`：左边界移动，右界不动，右边界就是最接近的
> - `mid > x / mid`：右边界向左移动，右边界也是最接近的

> ```c++
> int sqrt(int x) {
>         // write code here
>         if (x == 0) return 0;
>         int i = 1, j = x;
>         while (i <= j) {
>             int mid = i + (j-i)/2;
>             if (mid == x / mid) { // 乘法会有溢出问题
>                 return mid;
>             }
>             else if (mid > x / mid) {
>                 j = mid - 1;
>             }
>             else {
>                 i = mid + 1;
>             }
>         }
>         return j;
>     }
> ```



【NC42】**有重复项数字的所有排列**

> 关键在于去重原则：保证在填充第`i`个数时，该数字一定是这个数**所在的重复集合中第一个未被填充的数字**
>
> 例如：`[1a, 1b, 1c, 1d, 2]`构成`[1a, 1b, 1c, 2, 1d]`，下一次构造到`[1a, 1b, 1d]`时发现`1c`才是第一个未被填充的重复数字，直接剪枝。
>
> 也就是说，此举保护了重复数字填充的相对顺序，从前往后依次填充
>
> Link：基于交换元素的全排列（暂时无法去重）
>
> ```c++
> void dfs(vector<int> &nums, vector<vector<int>> &ans, int depth) {
>         if (depth >= nums.size()) {
>             ans.push_back(nums);
>             return;
>         }
>         for (int i = depth; i < nums.size(); ++i) {
>             if (i > depth && (nums[i] == nums[depth] || nums[i] == nums[i-1])) continue;
>             swap(nums[i], nums[depth]);
>             dfs(nums, ans, depth+1); // 注意传入的参数是depth+1
>             swap(nums[i], nums[depth]);
>         }
>     }
> ```
>
> 



【NC128】接雨水问题

【NC137】表达式求值

> 法1：双栈
>
> > 需要考虑运算符优先级
>
> - 符号栈记录符号
> - 操作数栈记录操作数

【NC92 最长公共子序列-II】

> LCS算法
>
> - 如何找到最长的序列？
>
>   从后往前遍历，找到上一个转移点
>
>   ![图片说明](https://uploadfiles.nowcoder.com/images/20210328/953626123_1616922024921/E320256E6BD7E5BC4E95A58DE0733B2E)

