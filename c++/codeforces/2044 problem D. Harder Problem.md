# [D. Harder Problem](https://codeforces.com/contest/2044/problem/D)

![image](https://github.com/user-attachments/assets/356724d1-78ee-4bdc-8249-53bd7164f694)

## Think of thought & Problem

这道题先读懂题意：根据一个数组 a , 创建一个数组 b , 使得 b 数组中的前 i 项数字的众数为 a[i] 。如果多个数字在 b 中的出现次数相同，那么众数可以是其中任意一位。

这道题一开始我是想的就是使 b 中每个的数字的出现次数尽可能相等，但是不知道怎么实现。后来又看了一眼题目，题目还有一个限制，我忽略了： a[i] <= n 其中 n 为 a 数组的大小。

然后我遇到一个问题，因为要尽可能使得每个数字的出现次数相等，那有没有可能这个数字出现了 2 次或多次呢？如果这样的话，我要怎么实现呢？

没有这种情况。因为 a[i] <= n , 所以一个大小为 n 的数组中可以满足这个数组中每个数字只出现一次。

然后问了一下 AI , AI 告诉我可以用一个 unused hashset 来实现。

也就是说先将数字 1 到 n 放入 unused 然后每遍历一个 a 中的元素，先判断这个元素是否是第一次出现，因为如果这个元素是第一次出现，那么对应的 b[i] 的位置就可以填入这个元素，也就是说这个元素是可以用的；如果不是第一次出现，那么对应的 b[i] 的位置就要在 unused 中选取一个元素填入；然后每次将填入的元素从 unused 中 erase 掉 。

判断元素出现的次数就可以用一个 cnt 数组来实现。

## Solution

```cpp
#include<bits/stdc++.h>
using namespace std;
void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    
    for(int i = 0; i < n; i++) {
        cin >> a[i];
    }

    vector<int> b(n);
    vector<int> cnt(n + 1, 0);
    unordered_set<int> unused;
    for(int i = 1; i <= n; i++) {
        unused.insert(i);
    }
    for(int i = 0; i < n; i++) {
        if(cnt[a[i]] == 0) {
            b[i] = a[i];
            cnt[a[i]]++;
            unused.erase(a[i]);
        }
        else {
            b[i] = *unused.begin();
            cnt[b[i]] ++;
            unused.erase(b[i]);
        }
    }
    for(int i = 0; i < n; i++) {
        cout << b[i] << " ";
    }
    cout << endl;
}
int main(){
    int t;
    cin >> t;
    while(t--) {
        solve();
    }
}
```
