# [C. Trinity](https://codeforces.com/contest/2032/problem/C)

![image](https://github.com/user-attachments/assets/2e32978a-2f73-4f05-a69d-8a3e66040b10)

## Train of thought

这道题是模仿灵神代码的思路来写的。

这道题的思路就是先对 a 数组进行排序，然后遍历 a[i] , 这里 a[i] 为三角形三条边中最长的边，所以还要再找两条边 x, y 使得 x + y > a[i] 。

因为数组已经排好序了，所以我只需要找到一个下标 j , 使得 a[j] + a[j + 1] > a[i] , 那么从 [j, i] 这个区间内任意三个值都可以构成三角形，需要修改的次数就是 n - (i - j + 1) 。

## Problem

一开始我并没有完全理解这个思路，我以为找到 a[j] 使得 a[j] + a[j + 1] > a[i] 后，剩下的元素可以取 [j, i] 区间的**任意值**。但是发现错误了。

这里举一个例子： 

a[i] = [1, 2, 6, 6, 6, 6, 7]

这里可以看出：a[1] + a[2] > a[6] 就是从[1, 6]这个区间里的值是不用变的，但是 a[0] 这个值必须要变，但是如果按我说的 a[0] 可以变成 [j, i] 区间的任意值，比如变成 2 ,

此时 a[i] = [2, 2, 6, 6, 6, 6, 7] , 很显然，这并不满足任意三个下标都可以构成三角形，(如果取了下标 0 和 1 , 那就一定构不成三角形) 。

难道是灵神思路错了？还是我没有理解灵神的思路？

还真是我没有理解灵神的思路，因为题目中说的是最少操作次数，所以这里 a[0] 的值是不能取**任意值**的。这里如果 a[0] = 6 或者 a[0] = 7 都是满足题意的。

## Solution

这是我一开始的代码：
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    vector<int> a(n);  // 读取输入数组
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    sort(a.begin(), a.end());  // 将数组排序
    int ans = n;  // 初始化答案为最多需要修改的次数（即 n）

    int j = 0;  // 使用双指针技巧
    for (int i = 2; i < n; i++) {  // 从小到大遍历 a[i]，作为三角形的最大边
        while (j + 1 < i && a[j] + a[j + 1] <= a[i]) {  // 找到满足 a[j] + a[j+1] > a[i] 的区间
            j++;
        }
        ans = min(ans, n - (i - j + 1));  // 更新操作次数
    }

    cout << ans << endl;
}

int main() {
    ios_base::sync_with_stdio(false);  // 提升输入输出效率
    cin.tie(nullptr);

    int t;
    cin >> t;  // 测试用例数量
    while (t--) {
        solve();  // 处理每个测试用例
    }

    return 0;
}
```
这个代码中 i 是**从大到小**枚举的，然后 j 的值是遍历出来的，但是提交的时候在第十二个案例超时了。

所以我想着，j 每次的值不要从 0 开始，因为 a[j] + a[j + 1] > a[i] , 并且 a[j + 1] >= a[j] , 那么 a[j + 1] 一定是 >= (a[i] + 1) / 2 的。这里就可以用lower_bound函数了。

这里我试了一下用 upper_bound 函数，部分案例是会出错的，因该是有些条件没考虑吧，直接用 lower_bound 就可以了，因为也不比 upper_bound 慢多少。
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    vector<int> a(n);  // 读取数组
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    sort(a.begin(), a.end());  // 排序
    int ans = n;  // 初始化答案为最多需要修改的次数

    for (int i = n - 1; i >= 2; i--) {  // 从大到小遍历最大边 a[i]
        int k = lower_bound(a.begin(), a.begin() + i, (a[i] + 1) / 2) - a.begin(); 
        // 找到第一个满足 a[k] >= a[i] / 2 的位置
        int j = max(0, k - 1);  // j 的初始位置可能是 k - 1
        while (j + 1 < i && a[j] + a[j + 1] <= a[i]) {
            j++;  // 调整 j 满足条件
        }
        // 更新最小操作次数
        ans = min(ans, n - (i - j + 1));
    }

    cout << ans << endl;
}

int main() {
    ios_base::sync_with_stdio(false);  // 提升输入输出效率
    cin.tie(nullptr);

    int t;
    cin >> t;  // 测试用例数量
    while (t--) {
        solve();  // 逐个测试用例处理
    }

    return 0;
}
```
这里的代码是 AI 帮我生成的，我试着自己写的，但是我没有注意到 ` int j = max(0, k - 1);  // j 的初始位置可能是 k - 1 ` 这一行，所以**越界**出错了。

然后就是题解中的方法，a[i] 都是**从小到大**遍历的。
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    vector<int> a(n);  // 读取输入数组
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    sort(a.begin(), a.end());  // 将数组排序
    int ans = n;  // 初始化答案为最多需要修改的次数（即 n）

    int j = 0;  // 使用双指针技巧
    for (int i = 2; i < n; i++) {  // 从小到大遍历 a[i]，作为三角形的最大边
        while (j + 1 < i && a[j] + a[j + 1] <= a[i]) {  // 找到满足 a[j] + a[j+1] > a[i] 的区间
            j++;
        }
        ans = min(ans, n - (i - j + 1));  // 更新操作次数
    }

    cout << ans << endl;
}

int main() {
    ios_base::sync_with_stdio(false);  // 提升输入输出效率
    cin.tie(nullptr);

    int t;
    cin >> t;  // 测试用例数量
    while (t--) {
        solve();  // 处理每个测试用例
    }

    return 0;
}
```
从大到小优化的代码是 AI 帮我生成的，其中 main 函数中用到了 ` ios_base::sync_with_stdio(false);  // 提升输入输出效率 cin.tie(nullptr); ` 这两段代码，这是用来提升输入输出效率的。

![image](https://github.com/user-attachments/assets/2554b85f-2f8a-4950-a9a8-1e038dd290a7)
