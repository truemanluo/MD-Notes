### Leetcode fast break

1. 两数之和

   > - 法1：用map存储满足条件的差值
   >   - `O(n)`空间+`O(n)`时间
   > - 法2：**排序**之后用**双指针**
   >   - `O(1)`空间+`O(nlogn)`时间

2. 两数相加（链表）

   > 模拟法
   >
   > **NOTE：**
   >
   > - 链表长度不一时可以类似统一处理：`num1 = l1 ? l1->val : 0;`
   > - 不要忘记最后的进位
   > - `l = l->next`之前先判断`l == nulptr`?

3. :star: 无重复字符的最长子串（字符串+滑动窗口）

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

4. :star:寻找两个正序数组的中位数

   > - 法1：归并
   >
   > - [ ] 法2：二分

5. :star:最长回文子串

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

6. 

- [ ] Z 字形变换

7. :star:整数反转

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

8. 字符串转换整数 (atoi)（同7）

9. 回文数

   > 核心：
   >
   > - 为了防止溢出，只反转数字的一半
   > - 判断反转一半的条件：`x_reverted >= x`
   > - 返回的条件：
   >   - 长度为奇数时：`x == x_reverted/10`
   >   - 长度为偶数时：`x == x_reverted`
   >
   > 例子：<img src="https://assets.leetcode-cn.com/solution-static/9/9_fig1.png" style="zoom: 50%;" />

11. 盛最多水的容器（双指针/贪心）

    > - 贪心策略：初始化`i = 0, j = n-1`，随着`i, j`向中间移动，容器的宽度是减少的，因此只有**移动`num[i],num[j]`之间的较小者**才有可能增大容积（短板效应）
    > - 双指针：指针由两侧向中间移动

12. 整数转罗马数字

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

13. 罗马数字转整数

    > 关键：只要左边对应的罗马数字小于其相邻右侧的罗马数字，则加上负的

14. 最长公共前缀

    > - 法1：横向搜索
    >
    > <img src="https://assets.leetcode-cn.com/solution-static/14/14_fig1.png" style="zoom: 25%;" />
    >
    > - 法2：纵向搜索（从字符串的每一列开始遍历）
    >
    >   注意终止条件：`i == strs[j].size() || strs[j][i] != c`（到达当前字符串长度时或者不等于当前字符）
    >
    > - 法3：分治

14. :star:三数之和

    > - 法：转化成两数之和+双指针
    >
    > 当我们需要枚举数组中的两个元素时，如果我们发现随着第一个元素的递增，第二个元素是递减的，那么就可以使用双指针的方法，将枚举的时间复杂度从 O(N^2)减少至 O(N).

15. :star:最接近的三数之和

    > - 法：与14类似，不同的是**直接算三数之和然后比较绝对值**

17. 电话号码的字母组合

    > - 法：递归生成combination
    >
    > Notes: 递归的参数是index，每个对应的index都要进行`[0, n-1]`的遍历

19. 删除链表的倒数第 N 个结点
20. 有效的括号
21. 合并两个有序链表