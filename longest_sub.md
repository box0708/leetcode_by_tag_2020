# 最长xx子yy问题集锦

## [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

```
Given an unsorted array of integers, find the length of longest increasing subsequence.

Example:

Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 
```
- dp解法，先遍历i: 0->N, 再遍历j: 0->i
- dp数组定义方法
```
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        if (nums.size() <= 1){
            return nums.size();
        }
        int n = nums.size();
        int res = INT_MIN;
        vector<int> dp(n, 1); // 以该位置结尾的最长子序列长度
        for ( int i=0; i < n; i++){
            for ( int j=0; j < i; j++){
                if (nums[i] > nums[j]){
                    dp[i] = max(dp[i], dp[j]+1);
                }
            }
            res = max(res, dp[i]);
        }
        return res;
    }
};
```