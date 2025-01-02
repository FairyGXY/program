# [C. Sagheer and Nubian Market](https://codeforces.com/contest/812/problem/C)

![image](https://github.com/user-attachments/assets/28700631-c9fd-4385-be74-20b071dc410a)

## Think of thought 

这道题的思路是问 ai 的，每个商品的价格是和购买商品的数量 k 和商品在数组 a 中的下标 i 有关系的，所以用二分的方法寻找 k , 然后针对每个购买商品的数量 k 计算出每个商品的价格，对价格排序，找出前 k 个商品的费用。

## Solution

```cpp
#include<bits/stdc++.h>
using namespace std;
long long getmincost(int k, vector<int> a) {
    vector<long long> cost(a.size());
    for(int i = 0; i < a.size(); i++) {
        cost[i] = a[i] + (long long)(i + 1) * k;
    }
    sort(cost.begin(), cost.end());
    long long mincost = 0;
    for(int i = 0; i < k; i++) {
        mincost += cost[i];
    }
    return mincost;
}

int main() {
    int n, S;
    cin >> n >> S;
    vector<int> a(n);
    for(int i = 0; i < n ; i++) {
        cin >> a[i];
    }

    int k = 0;
    long long T = 0;
    int left = 0, right = n;
    while(left <= right) {
        int mid = left + (right - left) / 2;
        long long cost = getmincost(mid, a);
        if(cost <= S) {
            k = mid;
            T = cost;
            left = mid + 1;
        }
        else {
            right = mid - 1;
        }
    }
    cout << k << " " << T << endl;
    return 0;
}
```
这里我定义了一个函数 getmincost 用来计算购买 k 个商品需要的最小花费，如果这个花费 <= 预算 S , 那么就更新 T 和 k 同时调整 left 。如果花费 > S 那么就调整 right 
