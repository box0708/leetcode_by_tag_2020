# 动态规划相关题目
## 单序列动态规划
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
---
## 双序列动态规划

### [***10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/) 
```
Given an input string (s) and a pattern (p), implement regular expression matching with support for '.' and '*' where: 

'.' Matches any single character.​​​​
'*' Matches zero or more of the preceding element.
The matching should cover the entire input string (not partial).

Example 1:

Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
Example 2:

Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
Example 3:

Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
Example 4:

Input: s = "aab", p = "c*a*b"
Output: true
Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore, it matches "aab".
Example 5:

Input: s = "mississippi", p = "mis*is*p*."
Output: false
```
- 见代码注释
- 异常输入要做处理，就算题干没说
```
class Solution {
public:
    bool isMatch(string s, string p) {
        int m=s.length(), n=p.length();
        if (n>0 && p[0] == '*'){
            return false; // *开头的是非法输入，但是oj里面也有，要做处理，否则不ac
        }
        // dp数组含义：dp[i][j] 表示s[0,i-1]与p[0,j-1]是否匹配
        vector<vector<bool>> dp(m+1, vector<bool>(n+1, false));
        dp[0][0] = true; // 空串匹配，coding技巧
        for ( int i=1; i <=m; i++){
            dp[i][0] = false; // p是空串，则无法匹配任何s
        }
        for ( int j=1; j <=n; j++){
            // 此处处理的问题，p为“x*---”这种。*代表不用x的时候，可以匹配空串
            if ( j>1 && '*' == p[j-1] && dp[0][j-2]){
                dp[0][j] = true;
            }
        }
        for ( int i=1; i < m+1; i++){
            for ( int j=1; j < n+1; j++){
                if ( p[j-1] != '*'){
                    // 最基本情况：无特殊符号，当前匹配的上 and 前一位置匹配的上
                    dp[i][j] = dp[i-1][j-1] && (p[j-1] == '.' || s[i-1] == p[j-1]);
                }
                else{
                    // p[j-1] == '*'时，设p[j-2]为字符x
                    // 情况a："---x*---"中，*代表x重复0次，即为dp[i][j-2] （x相当于没了）
                    // 情况b：*代表x重复至少1次，隐含为：“---xXX---”（大写表示重复出来的）
                    //      (s[i-1] == p[j-2] ||  '.' == p[j-2])表示p内*前一个（j-2位置）与x内i-1位置字符匹配
                    //      dp[i-1][j] 其实隐含了s内有重复字符。
                    //      例：s="abbba", p="ab*a"
                    //      当处理到*号时，若*前面的b与s[i-1]匹配，且s[0,i-2]与p[0,j-1]匹配
                    //      只能是 abb与ab*能匹配，abb后面的b能被*匹配，*代表b出现多次
                    dp[i][j] = dp[i][j-2] || (s[i-1] == p[j-2] ||  '.' == p[j-2]) && dp[i-1][j];
                }
            }
        }
        return dp[m][n];
    }
};
```
