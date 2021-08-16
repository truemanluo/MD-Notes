### 笔试题整理

1. 排雷问题：

> 在一块大小为$m*n$​的地图上，牛牛位于$(x1, y1)$​处，只能上下左右四个方向移动，每移动一个单位耗时一个单位时间，地雷位于$(x2, y2)$​​处​​。
>
> （1）地雷周围8个位置都是排雷点；
>
> （2）地图上存在不可达的点
>
> （3）保证$(0 <= x1, x2 < m)$​，$(0 <= y1, y2 < n)$​
>
> （4）’#‘表示位置不可达
>
> 求到达排雷点的最小时间，若不可到达任一排雷点，则返回-1

- 解析

  > 这是一个**起点、终点确定的求最小路径**的问题（属于多起点、多终点最短路径问题的特例，参考[Leetcode934](https://leetcode-cn.com/problems/shortest-bridge/)）
  >
  > 可用BFS解决：
  >
  > （1）首先将起点位置入队
  >
  > （2）取出队头，检测当前位置是否可排雷，是则返回结果， 否则从当前位置往四周扩散
  >
  > （3）每一步将四周可达的点加入队列中（在加入可达点之前可检测待加入的位置是否可排雷，是则返回结果steps+1），并**修改访问过的位置防止重复访问**
  >
  > （4）当前层遍历完成，步数+1
  >
  > （5）重复（2）-（4）

- 代码：

```c++
#include <iostream>
#include <vector>
#include <queue>
using namespace std;


class Solution
{
public:
    int timeConsume(vector<vector<char>> &grid, int start_x, int start_y, int end_x, int end_y) {
        // BFS: 从'.'出发，每次向四周扩散（为了防止重复遍历，修改访问过的节点）
        int steps = 0;
        m = grid.size();
        n = grid[0].size();
        vector<int> directions{0, 1, 0, -1, 0};
        queue<pair<int, int>> q;
        q.emplace(start_x, start_y);
        while (!q.empty()) {
            int sz = q.size();
            for (int i = 0; i < sz; ++i) {
                int r = q.front().first;
                int c = q.front().second;
                q.pop();
                // 检测是否可达到排雷点
                if (canReachDes(grid, r, c, end_x, end_y)) {
                    return steps;
                }
                // 向四周走一步
                for (int k = 0; k < 4; ++k) {
                    int x = r + directions[k];
                    int y = c + directions[k+1];
                    if (x < 0 || x >= m || y < 0 || y >= n || grid[x][y] == '#' || grid[x][y] == '*') continue; // 数组越界、目标不可达或者已访问过则跳过
                    // 检测是否可达到排雷点
                    if (canReachDes(grid, x, y, end_x, end_y)) {
                        return steps+1;
                    }
                    grid[x][y] = '*';
                    q.emplace(x, y);
                }
            }
            ++steps;    
        }
        return -1;
    }


private:
    int m, n;
    bool canReachDes(vector<vector<char>> &grid, int cur_x, int cur_y, int end_x, int end_y) {
        vector<int> around_dirctions{0, 1, 0, -1, 0}; 
        vector<int> arrow_dirctions{-1, -1, 1, 1, -1};

        // 上下左右四个方向
        for (int i = 0; i < 4; ++i) {
            if (cur_x+around_dirctions[i] == end_x && cur_y+around_dirctions[i+1] == end_y) {
                return true;
            }
        }

        // 左上、右上、左下、右下
        for (int j = 0; j < 4; ++j) {
            if (cur_x+arrow_dirctions[j] == end_x && cur_y+arrow_dirctions[j+1] == end_y) {
                return true;
            }
        }
        return false;
    }
};

void print(vector<vector<char>> &grid) {
    for (auto g : grid) {
        for (auto x : g) {
            cout << x << " ";
        }
        cout << endl;
    }
}

int main()
{
    vector<vector<char>> grid1 = {
        {'.', '.', '.', '.'},
        {'.', '.', '#', '.'},
        {'.', '#', '.', '#'},
        {'.', '.', '.', '#'}
    };
    vector<vector<char>> grid2 = {
        {'.', '.'},
        {'#', '.'}
    };
    vector<vector<char>> grid3 = {
        {'.', '.', '#'},
        {'.', '#', '.'},
        {'#', '.', '.'}
    };
    vector<vector<char>> grid4 = {
        {'.', '#', '.', '.'},
        {'.', '#', '#', '.'},
        {'.', '#', '.', '.'},
        {'.', '.', '.', '#'}
    };
    int start_x = 1, start_y = 1; // 从1编号，实际表示grid[0][0]
    int end_x = 3, end_y = 3;
    Solution s;
    cout << "Case1------------------------" << endl;
    print(grid1);
    cout << s.timeConsume(grid1, start_x-1, start_y-1, end_x-1, end_y-1) << endl;

    cout << "Case2------------------------" << endl;
    print(grid2);
    cout << s.timeConsume(grid2, 0, 0, 1, 1) << endl;

    cout << "Case3------------------------" << endl;
    print(grid3);
    cout << s.timeConsume(grid3, 0, 0, 2, 2) << endl;

    cout << "Case4------------------------" << endl;
    print(grid4);
    cout << s.timeConsume(grid4, 0, 0, 0, 2) << endl;
    return 0;
}
```

