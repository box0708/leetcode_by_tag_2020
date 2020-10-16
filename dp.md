# 动态规划相关题目

### [120. Triangle](https://leetcode.com/problems/triangle/)

> Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.
> 
> For example, given the following triangle

```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```
> The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).
- 正向/反向求解的变换
- 初始化起点/终点，求终点/起点
```
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        if ( triangle.empty() || triangle[0].empty()){
            return 0;
        }
        vector<vector<int>> dp(triangle);
        for ( int i=dp.size()-2; i >= 0; i--){
            // 最后一行开始
            for ( int j=0; j < dp[i].size(); j++){
                dp[i][j] += min(dp[i+1][j], dp[i+1][j+1]);
            }
        }
        return dp[0][0];
    }
};
```
---
### [132. Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/)
*sss dp+字符串*
```
Given a string s, partition s such that every substring of the partition is a palindrome.

Return the minimum cuts needed for a palindrome partitioning of s.

 

Example 1:

Input: s = "aab"
Output: 1
Explanation: The palindrome partitioning ["aa","b"] could be produced using 1 cut.
```
- 必会。回文：i-j已是回文，i-1，j+1若相等，也是回文
- coding细节 看注释
- 先遍历`0-i`，循环内遍历`0-j`,分`[0-j-1],[j-i]`求解
```
class Solution {
public:
    int minCut(string s) {
        if ( s.size() <= 1){
            return 0;
        }
        int n = s.size();
        vector<vector<bool>> helper(n, vector<bool>(n, false)); // 帮助计算回文
        vector<int> dp(n, 0); // dp数组，dp[i]代表【0-i】范围内的最小分割数
        
        for(int i=0; i < n; i++){
            dp[i] = i; // 最差情况，每个位置切一下，切成n块
            for ( int j=0; j <= i; j++){
                // 根据【j+1, i-1】范围上是否是回文，以及s[i],s[j]是否相等能判断出【j,i】上是否回文
                if ( s[i] == s[j] && ( i-j <= 1 || helper[j+1][i-1]) ){
                    // 发现j-i范围上回文，有可能产生更小的分割数，更新
                    helper[j][i] = true;
                    if ( j == 0){
                        dp[i] = 0; // j-i (0-i)整个都是回文的
                    }
                    else{
                        dp[i] = min(dp[i], dp[j-1]+1); // 0->j-1已知，j-1和j中间切一刀
                    }
                }
                
            }
        }
        return dp[n-1];
    }
};
```
---
### [647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
```
Given a string, your task is to count how many palindromic substrings in this string.

The substrings with different start indexes or end indexes are counted as different substrings even they consist of same characters.

Example 1:

Input: "abc"
Output: 3
Explanation: Three palindromic strings: "a", "b", "c".
 

Example 2:

Input: "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```
- 基本同上一题。dp解回文题的套路需要背
```
class Solution {
public:
    int countSubstrings(string s) {
        if (s.size() <=1){
            return s.size();
        }
        int n = s.size();
        vector<vector<bool>> dp(n, vector<bool>(n, false));
        int res=0;
        for ( int i=0; i < n; i++){
            for ( int j=0; j <= i; j++){
                if ( s[i] == s[j] && (i-j<2 || dp[j+1][i-1])){
                    dp[j][i] = true;
                    res += 1;
                }
            }
        }
        return res;
    }
};
```
---
### [139. Word Break](https://leetcode.com/problems/word-break/)
#### 需要多开一位的dp
```
Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words.

Note:

The same word in the dictionary may be reused multiple times in the segmentation.
You may assume the dictionary does not contain duplicate words.
Example 1:

Input: s = "leetcode", wordDict = ["leet", "code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
```
- 需要多开一个位置
- dp数组和原来串换算关系：以`i==8, j==4`为例，此时i代表原数组下标7的位置，j代表原数组下标3。j代表的下标+1为新串起点，长度为`(i-1) - (j-1 +1) +1` 
```
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        int n = s.size();
        // dp数组为n+1
        vector<bool> dp(n+1, false);
        dp[0] = true; // 起点
        for ( int i=1; i <=n; i++){
            // 基本同之前套路，但是此处i从1开始（其实是原字符串的0位置 i-1）
            for ( int j = 0; j < i; j++){ // j的边界控制
                if (dp[j]){ // 原字符串
                    string sub = s.substr(j, i-j); // 换算关系
                    if (find(wordDict.begin(), wordDict.end(), sub) != wordDict.end()){
                        dp[i] = true;
                        break;
                    }
                }
            }
        }
        return dp[n];
    }
};
```