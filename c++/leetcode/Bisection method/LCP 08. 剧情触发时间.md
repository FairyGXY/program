# [LCP 08. 剧情触发时间](https://leetcode.cn/problems/ju-qing-hong-fa-shi-jian/)

在战略游戏中，玩家往往需要发展自己的势力来触发各种新的剧情。一个势力的主要属性有三种，分别是文明等级（C），资源储备（R）以及人口数量（H）。在游戏开始时（第 0 天），三种属性的值均为 0。

随着游戏进程的进行，每一天玩家的三种属性都会对应增加，我们用一个二维数组 increase 来表示每天的增加情况。这个二维数组的每个元素是一个长度为 3 的一维数组，例如 [[1,2,1],[3,4,2]] 表示第一天三种属性分别增加 1,2,1 而第二天分别增加 3,4,2。

所有剧情的触发条件也用一个二维数组 requirements 表示。这个二维数组的每个元素是一个长度为 3 的一维数组，对于某个剧情的触发条件 c[i], r[i], h[i]，如果当前 C >= c[i] 且 R >= r[i] 且 H >= h[i] ，则剧情会被触发。

根据所给信息，请计算每个剧情的触发时间，并以一个数组返回。如果某个剧情不会被触发，则该剧情对应的触发时间为 -1 。

示例 1：

> 输入： increase = [[2,8,4],[2,5,0],[10,9,8]] requirements = [[2,11,3],[15,10,7],[9,17,12],[8,1,14]]  
> 输出: [2,-1,3,-1]  
> 解释：  
> 初始时，C = 0，R = 0，H = 0  
> 第 1 天，C = 2，R = 8，H = 4  
> 第 2 天，C = 4，R = 13，H = 4，此时触发剧情 0  
> 第 3 天，C = 14，R = 22，H = 12，此时触发剧情 2  
> 剧情 1 和 3 无法触发。  

示例 2：

> 输入： increase = [[0,4,5],[4,8,8],[8,6,1],[10,10,0]] requirements = [[12,11,16],[20,2,6],[9,2,6],[10,18,3],[8,14,9]]  
> 输出: [-1,4,3,3,3]  

示例 3：

> 输入： increase = [[1,1,1]] requirements = [[0,0,0]]  
> 输出: [0]  

限制：

> 1 <= increase.length <= 10000  
> 1 <= requirements.length <= 100000  
> 0 <= increase[i] <= 10  
> 0 <= requirements[i] <= 100000

## Think of thought

这道题的思路是 AI 告诉我的，主要思路就是：定义一个二维数组，里面存放每一天三种属性的累计值，然后遍历 requirements数组，用二分法找到满足 requirements 数组中三种属性所需的天数。

优化的思路是：在计算三种属性的累计值时，可以把所有属性的最大值求出来，然后在遍历 requirements 数组时，如果 requirements[i] 其中的一项属性 > 属性的最大值，此时这个剧情就不能触发。然后用灵神的二分法就可以
找到触发剧情的最短天数。

## Solution

```cpp
class Solution {
public:
    vector<int> getTriggerTime(vector<vector<int>>& increase, vector<vector<int>>& requirements) {
        int n = increase.size();
        vector<vector<int>> daily_increase(n + 1, vector<int>(3, 0));    // 属性累加值
        for(int i = 1; i <= n; i++) {
            for(int j = 0; j < 3; j++) {
                daily_increase[i][j] = daily_increase[i - 1][j] + increase[i - 1][j];
            }
        }
        int total_c = daily_increase[n][0],
            total_r = daily_increase[n][1],
            total_h = daily_increase[n][2];
        vector<int> ans(requirements.size(), -1);
        for(int i = 0; i < requirements.size(); i++) {
            int need_c = requirements[i][0],
                need_r = requirements[i][1],
                need_h = requirements[i][2];
            // 首先检查是否满足第 0 天的条件
            // if(need_c == 0 && need_r == 0 && need_h == 0) {
            //     ans[i] = 0;
            //     continue;    
            // }
            // 检查是否有可能满足条件
            if(need_c > total_c || need_r > total_r || need_h > total_h) continue;
            // 二分查找
            int left = 0, right = n;
            while(left <= right) {
                int mid = left + (right - left) / 2;
                if(daily_increase[mid][0] >= need_c && 
                    daily_increase[mid][1] >= need_r && 
                    daily_increase[mid][2] >= need_h) {
                    right = mid - 1;
                }
                else {
                    left = mid + 1;
                }
            }
            ans[i] = right + 1;  // 注意这里应该是 left，而不是 right + 1
        }
        return ans;
    }
};
```
代码中检查满足第 0 天的条件是可以省略的，如果不注释，也可以算作一个优化吧。
