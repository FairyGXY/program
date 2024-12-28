# [B. Born This Way](https://codeforces.com/contest/1148/problem/B)

![image](https://github.com/user-attachments/assets/cbd414f7-0671-45f1-936f-ecd80c769315)

## Train of thought

这道题可以分成两种情况来考虑：

1. 当 k >= m 或者 k >= n 时，此时可以直接去掉 A to B 或者 B to C 的所有航班，所以答案是 -1 .

2. 剩下的情况，用 i 来表示 A to B 第 i 个航班，例如 i = 0 ，表示 A to B 的第一次航班，即 A to B 这所有的航班中，我一个都没有取消。

   那么剩下的 k - i 的航班就是要从 B to C 的航班中去掉。设 left 为 A to B 的航班下标，` left = i + 1 - 1 ` (i + 1 的意思是：取消了前 i 个航班，要从第 i + 1 个航班开始，最后再 - 1 是因为航班的下标是从 0 开始)。

   设 right 为 B to C 最近的航班下标， ` right = lower_bound(b.begin(), b.end(), a[left] + ta) - b.begin() ` ，因为在 A to B 的航班中只取消了 i 个航班，所以要在 B to C 的航班中再取消 k - i 个航班。

   那么实际上 A to C 乘坐的航班下标为 left , B to C 乘坐的航班下标为 right + (k - i) 。

   这里还要对 right + (k - i) 判断，如果 ` right + (k - i) > m ` 那么此时剩下 B to C 的航班我都可以取消掉，是不能从 A to C 的，返回 -1

   这样，A to B 的时间为 ` a[left] + ta `

   A to C 的时间为 ` b[right + (k - i)] + tb `

## Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, m, ta, tb, k;
    cin >> n >> m >> ta >> tb >> k;

    vector<int> a(n);
    vector<int> b(m);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }
    for (int i = 0; i < m; i++) {
        cin >> b[i];
    }

    // 如果 k 超出范围则无解
    if (k >= n || k >= m) {
        cout << -1 << endl;
        return 0;
    }

    long long ans = -1; // 初始值设为 -1，这样方便判断无解的情况
    for (int i = 0; i <= k; i++) {
        int left = i; // 当前调整到第 i 个航班起飞
        //if (left >= n) break; // 必须保证 left 不越界

        int right = lower_bound(b.begin(), b.end(), a[left] + ta) - b.begin();
        if (right + (k - i) >= m) { // 检查剩余操作数会不会导致越界
            cout << -1 << endl;
            return 0;
        }

        ans = max(ans, 1LL * b[right + (k - i)] + tb); // 更新答案
    }

    cout << ans << endl;
    return 0;
}

```
