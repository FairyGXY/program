# [D. Inaccurate Subsequence Search](https://codeforces.com/contest/1955/problem/D)

![image](https://github.com/user-attachments/assets/5698b2f6-a18c-4321-8236-a2c27e8df99f)

## Solution 

简单概括题意：在长度为 n 的数组 a 中寻找长度为 m 的子数组，如果子序列中的元素与数组 b 中的元素至少有 k 个是相同的，那么这个子数组就是好数组，统计好数组个数。

这就是一道定长滑动窗口的题目。可以用 unordered_map<int, int> 来统计 b 中每个数字出现的次数，遍历数组 a 用 samecnt 来统计相同元素的个数，如果 samecnt >= k , ans++ 最后返回 ans 即可

## Problem

```cpp
#include<bits/stdc++.h>
using namespace std;
void solve() {
    int n, m, k;
    cin >> n >> m >> k;
    vector<int> a(n);
    vector<int> b(m);
    for(int i = 0; i < n; i++) {
        cin >> a[i];
    }
    for(int i = 0; i < m; i++) {
        cin >> b[i];
    }

    int ans = 0;
    unordered_map<int, int> mb;
    for(int i = 0; i < m; i++) {
        mb[b[i]] ++;
    }
    int samecnt = 0;
    for(int i = 0; i < n; i++) {
        if(mb.find(a[i]) != mb.end() && mb[a[i]] > 0) {
            samecnt++;
            mb[a[i]]--;
        }
        if(i < m - 1) {
            continue;
        }
        if(samecnt >= k) {
            ans ++;
        }
        int out = a[i - m + 1];
        if(mb.find(out) != mb.end()) {
            samecnt--;
            mb[out]++;
        }
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
这个是我一开始的代码，我是在哈希表 mb 的基础上修改的，但是答案一直是错误的，所以我去问了 AI 

![image](https://github.com/user-attachments/assets/0f8927fe-7c09-4f18-938f-c4d99787efd0)

就是说，如果此时 mba[i] 已经减为 0 了，但后面如果再出现 a[i] ，那么此时 samecnt 的值就不会改变，我当时想法是：mb[a[i]] == 0 说明此时虽然 a[i] 是 b 中的元素，但是已经把 b 中所有 a[i] 都匹配了，所以 samecnt 就不用改变了。

看了 AI 的解释后，经过深思熟虑，我发现了问题，虽然此时 a[i] 的元素确实匹配不了了，但是以后滑动窗口移除元素后呢？如果移除的元素刚好是 a[i] 那么此时，因为我没有将多的 a[i] 匹配，此时 samecnt-- ，但是正确的 samecnt 应该是不变的。

所以我试了 AI 的解法，多用了一个哈希表 window 来存储但我遍历过的元素，就不用在 mb 的基础上改动了。

## Solution

```cpp
#include <bits/stdc++.h>
using namespace std;
void solve() {
    int n, m, k;
    cin >> n >> m >> k;
    vector<int> a(n);
    vector<int> b(m);
    for(int i = 0; i < n; i++) {
        cin >> a[i];
    }
    for(int i = 0; i < m; i++) {
        cin >> b[i];
    }

    int ans = 0;
    unordered_map<int, int> mb;
    for(int x : b) {
        mb[x] ++;
    }
    unordered_map<int, int> window;
    int samecnt = 0;
    for(int i = 0; i < n; i++) {
        if(mb.find(a[i]) != mb.end()) {
            window[a[i]] ++;
            if(window[a[i]] <= mb[a[i]]) {
                samecnt++;
            }
        }
        
        if(i < m - 1) {
            continue;
        }
        if(samecnt >= k) {
            ans++;
        }
        int out = a[i - m + 1];
        if(mb.find(out) != mb.end()) {   
            if(window[out] <= mb[out]) {
                samecnt--;
            }
            window[out]--;
        }
    }
    cout << ans << endl;;
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
退化了，刷了这么多滑动窗口的题目，现在一个定长滑动窗口写不出啦🥲
