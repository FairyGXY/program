# [2563. 统计公平数对的数目](https://leetcode.cn/problems/count-the-number-of-fair-pairs/description/)
```
给你一个下标从 0 开始、长度为 n 的整数数组 nums ，和两个整数 lower 和 upper ，返回 公平数对的数目 。

如果 (i, j) 数对满足以下情况，则认为它是一个 公平数对 ：

0 <= i < j < n，且
lower <= nums[i] + nums[j] <= upper
 

示例 1：

输入：nums = [0,1,7,4,4,5], lower = 3, upper = 6
输出：6
解释：共计 6 个公平数对：(0,3)、(0,4)、(0,5)、(1,3)、(1,4) 和 (1,5) 。
示例 2：

输入：nums = [1,7,9,2,5], lower = 11, upper = 11
输出：1
解释：只有单个公平数对：(2,3) 。
 

提示：

1 <= nums.length <= 105
nums.length == n
-109 <= nums[i] <= 109
-109 <= lower <= upper <= 109
```
这是我代码：
```cpp
class Solution {
public:
    long long countFairPairs(vector<int>& nums, int lower, int upper) {
        long long ans = 0;
        ranges::sort(nums);
        for(int i = 0; i < nums.size(); i++){
            int start = lower_bound(nums.begin() + i + 1,nums.end(),lower - nums[i]) - nums.begin();
            if(start != nums.size()){
                int end = lower_bound(nums.begin() + i + 1,nums.end(),upper - nums[i] + 1) - nums.begin() - 1;
            if(start <= end)
                ans += (end - start + 1);
            }
        }
        return ans;
    }
};
```
这道题思路简单： lower <= nums[i] + nums[j] <= upper ，所以我只要遍历 nums[i] ，然后寻找满足 lower - nums[i] <= nums[j] <= upper - nums[i] 的 nums[j]的个数就可以了。 

先排序，然后找第一个大于等于 lower - nums[i] 的下标 `int start = lower_bound(nums.begin() + i + 1,nums.end(),lower - nums[i]) - nums.begin();` 

这里是要在 `[nums.begin() + i + 1, nums.end())` 区间找的，因为如果是在 `[nums.begin(),nums.end())` 这个区间，会重复。

一开始我想到了重复的情况，写的是 `[nums.begin() + i, nums.end())` 但是这样的话可能会指向同一个下标，也就是返回 (1,1) 这种的，不符合题意。后面AI提醒我了。

然后找到第一个 > upper - nums[i] 的下标，这里我用的是灵神视频中教的写法，也可以直接写成 `upper_bound(nums.begin() + i + 1,nums.end(),upper - nums[i]) - 1`。

然后灵神的[题解](https://leetcode.cn/problems/count-the-number-of-fair-pairs/solutions/2107079/er-fen-cha-zhao-de-ling-huo-yun-yong-by-wplbj)应该是反过来的，遍历的是 nums[j] ，代码是这个：**我没有仔细看，懒了😅😅**
```cpp
class Solution {
public:
    long long countFairPairs(vector<int> &nums, int lower, int upper) {
        long long ans = 0;
        ranges::sort(nums);
        for (int j = 0; j < nums.size(); ++j) {
            auto r = upper_bound(nums.begin(), nums.begin() + j, upper - nums[j]); // <= upper-nums[j] 的 nums[i] 的个数
            auto l = lower_bound(nums.begin(), nums.begin() + j, lower - nums[j]); // < lower-nums[j] 的 nums[i] 的个数
            ans += r - l;
        }
        return ans;
    }
};
```
