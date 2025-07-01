## 思路描述

- 初始化一个大小为 $H \times W$ 的二维数组 `vis`，所有元素初始为 $0$，表示未访问过。
- 从起点 `(1, 1)` 开始，记录两个变量 $x=1,y=1$。：
  - 如果当前位置 `vis[i][j]` 已经是 $1$，说明之前访问过，输出 `-1` 并结束程序。
  - 否则，将 `vis[i][j]` 标记为 $1$，继续根据当前位置的方向移动。
- 移动时，根据当前格子的方向字符（`U`、`D`、`L`、`R`）决定下一步坐标：
  - `U`：`x - 1, y`
  - `D`：`x + 1, y`
  - `L`：`x, y - 1`
  - `R`：`x, y + 1`
- 如果移动后的位置超出边界，停止并输出当前位置的坐标。
- 重复上述过程直到结束。

---


## 实现示例


```cpp
#include <bits/stdc++.h>
using namespace std;
int h, w;
char g[505][505];
bool vis[505][505];
int main()
{
    cin >> h >> w;
    for (int i = 1; i <= h; i++)
    {
        for (int j = 1; j <= w; j++)
        {
            cin >> g[i][j]; // 注意用字符数组
        }
    }
    int x = 1, y = 1; // 起始位置
    while (true)
    {
        if (vis[x][y] == 1) // 已经访问过直接结束即可
        {
            cout << "-1";
            return 0;
        }
        vis[x][y] = 1; // 打标记
        if (g[x][y] == 'U')
        {
            if (x - 1 >= 1) // 往上走不越界
                x--;
            else // 越界了就直接输出
            {
                cout << x << " " << y;
                return 0;
            }
        }
        else if (g[x][y] == 'D')
        {
            if (x + 1 <= h) // 往下走不越界
                x++;
            else // 越界了直接输出
            {
                cout << x << " " << y;
                return 0;
            }
        }
        else if (g[x][y] == 'L')
        {
            if (y - 1 >= 1) // 往左走不越界
                y--;
            else // 越界了直接输出
            {
                cout << x << " " << y;
                return 0;
            }
        }
        else if (g[x][y] == 'R')
        {
            if (y + 1 <= w) // 往右走不越界
                y++;
            else // 越界了直接输出
            {
                cout << x << " " << y;
                return 0;
            }
        }
    }
}
```

## 注意

不要写多个 `if` 语句，避免一次移动执行多个条件的代码。