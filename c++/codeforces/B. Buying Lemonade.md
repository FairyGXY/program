# [B. Buying Lemonade](https://codeforces.com/problemset/problem/2024/B)

![image](https://github.com/user-attachments/assets/b4877381-d1d2-4a3b-8476-e8faf32599bd)

这道题的思路很好想出来，以 n = 3, k = 4, a = [2, 1, 3] 为例子：

先对数组进行排序 a = [1, 2, 3] 然后找到上一次 > 0 位置的下标 pre_index , 这一次 > 0 位置的下标 index , 此时， index - pre_index 就是我这一次按了按纽，但是没有掉落苏打水的次数。这里定义为 wrong_count 。

这时， n - index 代表还有苏打水的槽数，所以按一次之后，获得的苏打水的个数为 n - index 所以 k -= n - index , 按按钮的次数为 n - index + wrong_count 所以 ans += n -index + wrong_count。

当 a[index] * (n - index) < k 时，这时说明 剩下的按钮，我每次都按一遍，直到获得的苏打水个数为 k , index 也不会在改变。这是只需要 ans += k ，然后终止循环就可以。下面的是我的代码：

```cpp
#include<bits/stdc++.h>
using namespace std;
void solve() {
    long long n, k;
    long long ans = 0;
    cin >> n >> k;
    vector<int> a(n);
    for(int i = 0; i < n; i++){
        cin >> a[i];
    }
    sort(a.begin(), a.end());
    
    int pre_index = 0;
    int index = 0;
    int wrong_count = 0;
    while(k > 0) {
        ans += wrong_count;
        if(a[index] * (n - index) >= k) {
            ans += k;
            k = 0;
            break;
        }
        for(int i = index; i < n; i++) {
            a[i] -= a[index];
        }
        k -= (n - index) * a[index];
        ans += (n - index) * a[index];
        index = upper_bound(a.begin(), a.end(), 0) - a.begin();
        wrong_count = index - pre_index;
        pre_index = index;
    }
    cout << ans << endl;
}
int main() {
    int t;
    cin >> t;
    while(t--) {
        solve();
    }
    return 0;
}
```
因为题目上面说 a[i] >= 1 ，所以第一次 pre_index 直接初始化为 0 ，while 循环里面，先加上错误的次数，然后判断 ` if(a[index] * (n - index) >= k) ` , 然后数组每次减去 a[index] 再找下一次的 index 

但是我的代码是**超时**的，因为每次都要操作数组减去 a[index] ，最坏的情况下时间复杂度为 O(n^2) 后面让 AI 改了一下，AI 给出的解决办法挺好的，这是改之后的代码：
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    long long n, k;    // n 槽的数量，k 总需求（柠檬水）
    long long ans = 0; // 最终的按键次数
    cin >> n >> k;
    vector<int> a(n);  // n 个槽的初始容量
    for (int i = 0; i < n; i++) {
        cin >> a[i];
    }

    // 1. 先排序数组 (从小到大)
    sort(a.begin(), a.end());

    int index = 0, pre_index = 0; // 当前的索引和上一次大于0位置的索引
    long long prev = 0;           // 累积的减小值
    int wrong_count = 0;          // 上次按错的按钮数量，只是无效消耗

    while (k > 0) {               // 循环直到饮料总数减少到0
        ans += wrong_count;       // 累加按错的次数（不产生饮料却按下的次数）

        // 当前“有效差值”：所有槽相对上一次基准的差值
        long long diff = a[index] - prev;

        // 如果当前这一步能够完成 `k` 的需求，则直接退出
        if (diff * (n - index) >= k) {
            ans += k;             // 增加完成k需求
            k = 0;                // k 减为 0，退出
            break;
        }

        // 如果当前这一步不能满足需求
        k -= diff * (n - index);  // 消耗掉当前有效按钮导致的饮料
        ans += diff * (n - index);// 更新总按键次数（所有有效按钮）

        prev = a[index];          // 更新“累积减小值”

        // 跳过已经耗尽的槽，找到下一个还未耗尽的槽
        while (index < n && a[index] == prev) {
            index++;
        }

        // 更新按错的按钮数量
        wrong_count = index - pre_index;
        pre_index = index;
    }

    cout << ans << endl;          // 输出最终结果
}

int main() {
    int t; // 测试用例数量
    cin >> t;
    while (t--) {
        solve(); // 执行每个测试用例
    }
    return 0;
}
```
这里用了一个有效差值 diff 是逻辑上减少的值，这样就不用在对数组中每个元素减少 a[index] 了。
```
// 跳过已经耗尽的槽，找到下一个还未耗尽的槽
while (index < n && a[index] == prev) {
    index++;
}
```
这一段代码就相当于我的代码中的 ` index = upper_bound(a.begin(), a.end(), 0) - a.begin(); ` 这一段。并且这个 index < n 这个逻辑， AI 说是不能少的，但是我去掉后，还是可以 AC 的，可能是 AI 没有看清题目的条件。
