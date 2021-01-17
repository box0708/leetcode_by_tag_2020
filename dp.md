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
```c++
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
## dp解决回文串问题
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
```c++
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
```c++
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
### [32. Longest Valid Parentheses 最长合格括号字串问题](https://leetcode-cn.com/problems/longest-valid-parentheses/)
> Given a string containing just the characters `'('` and `')'`, find the length of the longest valid (well-formed) parentheses substring.
> 
> Example 2:
> 
> Input: s = `")()())"`
> 
> Output: 4
> 
> Explanation: The longest valid parentheses substring is `"()()"`.
- 与回文串问题相似，但不完全相同，见注释
- 为什么不达标时候`dp[i]`置0：dp数组算的是以某一位置**结尾**的xxxx。
```c++
class Solution {
public:
    int longestValidParentheses(string s) {
        if(s.size() <= 1){
            return 0;
        }
        int n = s.size(), res=0;
        vector<int> dp(n+1, 0); // dp[i]: 以s[i-1]结尾的字符串中，符合要求的最长字串长度
        for (int i=1; i < n+1; i++){
            /*
                j下标含义：能知道dp[i-1]，也就是s[i-2]结尾的字符串中最长字串长度
                那么往回倒，s[j]是要和s[i-1]匹配的对应下标
            */
            int j = (i-1) - dp[i-1] -1; 
            if (j < 0 || s[i-1] != ')' || s[j] != '('){
                dp[i] = 0; // 如果不满足匹配，那么置0
            }
            else{
                /*
                    s[j]和s[i-1]匹配上，更新dp[i]，三部分拼起来
                    dp[i-1]: s[j+1]到s[i-2]的满足要求字串长度
                    2: s[j]和s[i-1]增加的往两边扩的长度
                    dp[j]: s[j-1]结尾的满足要求长度
                */
                dp[i] = dp[i-1] + 2 + dp[j];
                res = max(res, dp[i]);
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
```c++
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
---
### [44. Wildcard Matching](https://leetcode.com/problems/wildcard-matching/)
```
Given an input string (s) and a pattern (p), implement wildcard pattern matching with support for '?' and '*' where:

'?' Matches any single character.
'*' Matches any sequence of characters (including the empty sequence).
The matching should cover the entire input string (not partial).

Example 1:

Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
Example 2:

Input: s = "aa", p = "*"
Output: true
Explanation: '*' matches any sequence.
Example 3:

Input: s = "cb", p = "?a"
Output: false
Explanation: '?' matches 'c', but the second letter is 'a', which does not match 'b'.
Example 4:

Input: s = "adceb", p = "*a*b"
Output: true
Explanation: The first '*' matches the empty sequence, while the second '*' matches the substring "dce".
Example 5:

Input: s = "acdcb", p = "a*c?b"
Output: false
```
- 基本同上一题方法
- `dp[i][j] = dp[i][j-1] || dp[i-1][j];`中，前一个条件代表`*为空串`，相当于`p[j-1]`没有了，则检查p串前一位；后一个条件代表`*代替任何串`，则只要s串前一位能匹配上，s串当前位置愿意是啥是啥
- tmd做完上一题这个要是不ac的真的可以去死了
```
class Solution {
public:
    bool isMatch(string s, string p) {
        int m=s.size(), n=p.size();
        vector<vector<bool>> dp(m+1, vector(n+1, false));
        dp[0][0] = true; // 空串匹配空串
        // 初始化边界条件
        for ( int i=1; i < m+1; i++){
            dp[i][0] = false; // p空串无法匹配任何串
        }
        for ( int j=1; j < n+1; j++){
            if ( '*' == p[j-1]){
                dp[0][j] = true; // 只要有*就能匹配s空串
            }
            else{
                break;
            }
        }
        for ( int i=1; i < m+1; i++){
            for ( int j=1; j < n+1; j++){
                if (p[j-1] != '*'){
                    dp[i][j] = (s[i-1] == p[j-1] || p[j-1] == '?') && dp[i-1][j-1];
                }
                else{
                    dp[i][j] = dp[i][j-1] || dp[i-1][j];
                }
            }
        }
        return dp[m][n];
    }
};
```
---
### [72. Edit Distance](https://leetcode.com/problems/edit-distance/)
```
Given two words word1 and word2, find the minimum number of operations required to convert word1 to word2.

You have the following 3 operations permitted on a word:

Insert a character
Delete a character
Replace a character
Example 1:

Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```
- 见注释
```
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m=word1.size(), n=word2.size();
        
        vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
        for ( int i=1; i < m+1; i++){
            dp[i][0] = i;
        }
        for ( int j=1; j < n+1; j++){
            dp[0][j] = j;
        }
        for ( int i=1; i < m+1; i++){
            for ( int j=1; j < n+1; j++){
                if (word1[i-1] == word2[j-1]){
                    dp[i][j] = dp[i-1][j-1]; // 字符相同，不解释
                }
                else{
                    // dp[i-1][j-1] + 1: replace
                    // dp[i][j-1]+1: insert, word2[j-1]被insert进来的解决
                    // dp[i-1][j]+1: delete, word1[i-1]相当于没了
                    dp[i][j] = min(dp[i-1][j-1] + 1, min(dp[i][j-1]+1, dp[i-1][j]+1));
                }
            }
        }
        return dp[m][n];
    }
};
```
---
### [97. Interleaving String](https://leetcode.com/problems/interleaving-string/)
```
Given strings s1, s2, and s3, find whether s3 is formed by an interleaving of s1 and s2.

An interleaving of two strings s and t is a configuration where they are divided into non-empty substrings such that:

s = s1 + s2 + ... + sn
t = t1 + t2 + ... + tm
|n - m| <= 1
The interleaving is s1 + t1 + s2 + t2 + s3 + t3 + ... or t1 + s1 + t2 + s2 + t3 + s3 + ...
Note: a + b is the concatenation of strings a and b.

Example 1:
Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
Output: true

Example 2:
Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
Output: false

Example 3:
Input: s1 = "", s2 = "", s3 = ""
Output: true
```
- 看注释
- 此类题目高度套路化，不要想复杂
```
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        if ( s1.size() + s2.size() != s3.size()){
            // 隐含条件要考虑，否则不ac
            return false;
        }
        int m=s1.size(), n=s2.size();
        vector<vector<bool>> dp(m+1, vector<bool>(n+1, false));
        dp[0][0] = true;
        
        for ( int i=1; i < m+1; i++){
            // 只用s1拼s3
            dp[i][0] = s1[i-1] == s3[i-1] && dp[i-1][0];
        }
        for ( int j=1; j < n+1; j++){
            // 只用s2拼s3
            dp[0][j] = s2[j-1] == s3[j-1] && dp[0][j-1];
        }
        for ( int i=1; i < m+1; i++){
            for ( int j=1; j < n+1; j++){
                // 分别考虑用s1/s2当前字符匹配s3对应位置字符，此时另一个没用的字符串的最新位置没参与匹配，s3下标需要-1
                dp[i][j] = (s1[i-1] == s3[i-1+j] && dp[i-1][j]) || (s2[j-1] == s3[i+j-1] && dp[i][j-1]);
            }
        }
        return dp[m][n];
    }
};
```
---
### [712. Minimum ASCII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/)
```
Given two strings s1, s2, find the lowest ASCII sum of deleted characters to make two strings equal.

Example 1:
Input: s1 = "sea", s2 = "eat"
Output: 231
Explanation: Deleting "s" from "sea" adds the ASCII value of "s" (115) to the sum.
Deleting "t" from "eat" adds 116 to the sum.
At the end, both strings are equal, and 115 + 116 = 231 is the minimum sum possible to achieve this.
Example 2:
Input: s1 = "delete", s2 = "leet"
Output: 403
Explanation: Deleting "dee" from "delete" to turn the string into "let",
adds 100[d]+101[e]+101[e] to the sum.  Deleting "e" from "leet" adds 101[e] to the sum.
At the end, both strings are equal to "let", and the answer is 100+101+101+101 = 403.
If instead we turned both strings into "lee" or "eet", we would get answers of 433 or 417, which are higher.
```
```
class Solution {
public:
    int minimumDeleteSum(string s1, string s2) {
        int m=s1.size(), n=s2.size();
        vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
        // 有一个是空串
        for ( int i=1; i < m+1; i++){
            // dp[i][0]
            dp[i][0] = dp[i-1][0] + s1[i-1];
        }
        for ( int j=1; j < n+1; j++){
            // dp[0][j]
            dp[0][j] = dp[0][j-1] + s2[j-1];
        }
        for ( int i=1; i < m+1; i++){
            for ( int j=1; j < n+1; j++){
                if (s1[i-1] != s2[j-1]){
                    dp[i][j] = min(s1[i-1] + dp[i-1][j], s2[j-1] + dp[i][j-1]);
                }
                else{
                    dp[i][j] = dp[i-1][j-1];
                }
            }
        }
        return dp[m][n];
    }
};
```
---
### [115. Distinct Subsequences](https://leetcode-cn.com/problems/distinct-subsequences/)
> Given two strings `s` and `t`, return the number of distinct subsequences of `s` which equals `t`.
> 
> A string's subsequence is a new string formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (i.e., `"ACE"` is a subsequence of `"ABCDE"` while `"AEC"` is not).
> 
> It's guaranteed the answer fits on a 32-bit signed integer.
- 注意总结
```c++
class Solution {
public:
    int numDistinct(string s, string t) {
        int m=s.size(), n=t.size();
        vector<vector<long>> dp(m+1, vector<long>(n+1, 0));
        dp[0][0] = 1; // 空串匹配空串，1个子序列
        for ( int i=1; i < m+1; i++){
            dp[i][0] = 1; // s串匹配空串，只有1个子序列
        }
        // dp[0][j] (j>=1) 默认为0，因为s空串时无法找出任何子序列匹配t串
        for ( int i=1; i < m+1; i++){
            for ( int j=1; j < n+1; j++){
                // dp[i][j] 表示s串[0, i-1]的子串里找子序列，匹配t串[0, j-1]的子串
                // 选择分支：选不选s[i-1]字符到当前匹配
                if (s[i-1] == t[j-1]){
                    /*
                        s[i-1] == t[j-1], 此时有两个分支
                        1）用当前位置字符s[i-1], 则当前结果加上dp[i-1][j-1], 
                        相当于s[i-1]解决了t[j-1]位置字符匹配
                        2）不用当前位置字符s[i-1]，则加上s[0 -> i-2] 匹配 t[0 -> j-2]结果
                    */
                    dp[i][j] = dp[i-1][j-1] + dp[i-1][j];
                }
                else{
                    // 相当于上面的分支2）逻辑
                    dp[i][j] = dp[i-1][j];
                }
            }
        }
        return dp[m][n];
    }
};
```
---
## 股票类dp问题(局部最优+全局最优)

### [188. Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)
```
You are given an integer array prices where prices[i] is the price of a given stock on the ith day.

Design an algorithm to find the maximum profit. You may complete at most k transactions.

Notice that you may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

Example 1:

Input: k = 2, prices = [2,4,1]
Output: 2
Explanation: Buy on day 1 (price = 2) and sell on day 2 (price = 4), profit = 4-2 = 2.
Example 2:

Input: k = 2, prices = [3,2,6,5,0,3]
Output: 7
Explanation: Buy on day 2 (price = 2) and sell on day 3 (price = 6), profit = 6-2 = 4. Then buy on day 5 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
```
- 此题为通解。[参考链接](https://www.jianshu.com/p/26f792f83ee4)
- `local[i][j]`代表局部最优：第`i`天最多`j`次交易最大利润
- `global[i][j]`全局最优
- 二维dp数组中`k`对应的那一维多开一位，同上面总结的题目
- *一买，一卖，算一次交易*
- `local[i][j] = max( global[i-1][j-1] + max(0, diff), local[i-1][j] + diff);`更新方程的理解
  - `global[i-1][j-1] + max(0, diff)`: 第i-1天，已经交易了j-1次。在第i天要完成一次交易。如果能赚钱，i-1买i卖；如果不能，i买i卖（当天交易）
  - `local[i-1][j] + diff`: 第i-1天已经完成了j-1次交易，且第j次交易的买入在i-1天；此时第i天完成第j次交易的卖出。
```
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if ( k <= 0 || prices.size() < 2){
            return 0;
        }
        int n = prices.size(); // 天数
        if (k >= n/2){
            // 任意次交易
            int res = 0;
            for ( int i=1; i < n; i++){
                int diff = prices[i] - prices[i-1];
                if ( diff > 0){
                    res += diff;
                }
            }
            return res;
        }
        else{
            vector<vector<int>> local(n, vector<int>(k+1, 0));
            vector<vector<int>> global(n, vector<int>(k+1, 0));
            for ( int i=1; i < n; i++){
                int diff = prices[i] - prices[i-1];
                for ( int j=1; j < k+1; j++){
                    local[i][j] = max( global[i-1][j-1] + max(0, diff), local[i-1][j] + diff);
                    global[i][j] = max(local[i][j], global[i-1][j]);
                }
            }
            return global[n-1][k];
        }
    }
};
```
---
## 背包类dp问题

### 01背包问题

> Q：有 N 件物品和一个容量为 V 的背包。放入第 i 件物品耗费的费用是 $C_i$，得到的 价值是 $W_i$。求解将哪些物品装入背包可使价值总和最大。

A：dp解法。`dp[i][j]`表示前i件物品放入容量为j的背包可以获得最大价值
```
    dp[i][j] = max(dp[i-1][j], dp[i-1][j-c[i]] + w[i]) // 放 or 不放
```

### 完全背包问题

> Q：与01背包相同，物品可以使用无限次
```
    dp[i][j] = max(dp[i-1][j], dp[i][j-c[i]] + w[i]) // 放 or 不放    
```

### [518. Coin Change 2](https://leetcode.com/problems/coin-change-2/)
```
You are given coins of different denominations and a total amount of money. Write a function to compute the number of combinations that make up that amount. You may assume that you have infinite number of each kind of coin.

Example 1:

Input: amount = 5, coins = [1, 2, 5]
Output: 4
Explanation: there are four ways to make up the amount:
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```
- 做过的题注意总结
- 边界条件处理
```
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        if (coins.size() == 0){
            // 不给硬币 只能换出0元
            if (amount == 0){
                return 1;
            }
            else{
                return 0;
            }
        }
        int m = coins.size();
        // 开dp数组。dp[i][j]表示用coins[i]，换j金额的换法
        vector<vector<int>> dp(m, vector<int>(amount+1, 0));
        dp[0][0] = 1; // 初始条件
        // 只用第0种硬币，整除才能换
        for ( int money=0; money <= amount; money += coins[0]){
            dp[0][money] = 1;
        }
        for ( int i=1; i < m; i++){
            for ( int j=0; j < amount+1; j++){
                for ( int k=0; j-k*coins[i] >= 0; k++){
                    // coins[i]选k个
                    dp[i][j] += dp[i-1][j-k*coins[i]];
                }
            }
        }
        return dp[m-1][amount];
    }
};
```

### [416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)
```
Given a non-empty array nums containing only positive integers, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

Example 1:

Input: nums = [1,5,11,5]
Output: true
Explanation: The array can be partitioned as [1, 5, 5] and [11].
```
- 转化为背包问题来解
- 恰好装满，初始化条件与最大值不同
- 第二层循环的遍历顺序，要倒序，因为不能覆盖前面的值（二维dp压成一维之后，每次进num循环时，存的值相当于二维dp中上一行的值）
```
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        if (nums.size() <= 1){
            return false;
        }
        int sum=0;
        for ( int i=0; i < nums.size(); i++){
            sum += nums[i];
        }
        if (sum % 2 == 1){
            return false;
        }
        int target = sum/2;
        vector<bool> dp(target + 1, false); // 初始化条件
        dp[0] = true;
        for( int num : nums){
            for ( int j = target; j >= num; j--){
                dp[j] = dp[j] || dp[j-num]; // 此处需要逆向遍历，不能覆盖之前的值
            }
        }
        return dp[target];
    }
};
```

### [474. Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/)
```
You are given an array of binary strings strs and two integers m and n.

Return the size of the largest subset of strs such that there are at most m 0's and n 1's in the subset.

A set x is a subset of a set y if all elements of x are also elements of y.

Example 1:

Input: strs = ["10","0001","111001","1","0"], m = 5, n = 3
Output: 4
Explanation: The largest subset with at most 5 0's and 3 1's is {"10", "0001", "1", "0"}, so the answer is 4.
Other valid but smaller subsets include {"0001", "1"} and {"10", "1", "0"}.
{"111001"} is an invalid subset because it contains 4 1's, greater than the maximum of 3.
```
- 定义转移方程。一般来说让求什么值，dp状态就是这个值。此题是遍历字符串，dp值为数量
- 循环，反向
```
class Solution {
public:
    int findMaxForm(vector<string>& strs, int m, int n) {
        vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
        // dp[i][j] : i个0，j个1，能组成几个字符串（子集大小）
        for ( string str : strs){
            int zeros=0, ones=0;
            for (char c : str){
                if (c == '0'){
                    zeros += 1;
                }
                else{
                    ones += 1;
                }
            }
            for ( int i=m; i >= zeros; i--){
                for ( int j=n; j >= ones; j--){
                    dp[i][j] = max(dp[i][j], dp[i-zeros][j-ones]+1);
                }
            }
        }
        return dp[m][n];
    }
};
```

---

## 博弈型dp

### [486. Predict the Winner](https://leetcode.com/problems/predict-the-winner/)
```
Given an array of scores that are non-negative integers. Player 1 picks one of the numbers from either end of the array followed by the player 2 and then player 1 and so on. Each time a player picks a number, that number will not be available for the next player. This continues until all the scores have been chosen. The player with the maximum score wins.

Given an array of scores, predict whether player 1 is the winner. You can assume each player plays to maximize his score.

Example 1:

Input: [1, 5, 2]
Output: False
Explanation: Initially, player 1 can choose between 1 and 2. 
If he chooses 2 (or 1), then player 2 can choose from 1 (or 2) and 5. If player 2 chooses 5, then player 1 will be left with 1 (or 2). 
So, final score of player 1 is 1 + 2 = 3, and player 2 is 5. 
Hence, player 1 will never be the winner and you need to return False.
```
- 博弈型经典题
- 先手/后手的收益值列出来，注意带‘绝顶聪明’条件，min/max的写法
```
class Solution {
public:
    // 递归写法
    int first(vector<int>& nums, int i, int j){
        // 先拿，i-j范围上最大收益
        if (i==j){
            return nums[i]; // 直接拿
        }
        else{
            // 长度大于2，拿走两边后变成后手，调second函数
            return max(nums[i] + second(nums, i+1, j), nums[j] + second(nums, i, j-1));
        }
    }
    
    int second(vector<int>& nums, int i, int j){
        if ( i == j){
            return nums[i];
        }
        else{
            // 此时只可能有最小的收益，用min
            return min(first(nums, i+1, j), first(nums, i, j-1));
        }
    }
    
    
    bool PredictTheWinner(vector<int>& nums) {
        if (nums.size() == 0){
            return false;
        }
        if (nums.size() == 1){
            return true;
        }
        int sum = 0;
        for ( int i : nums){
            sum += i;
        }
        
        int res = first(nums, 0, nums.size()-1);
        if ( res >= sum-res){
            return true;
        }
        return false;
        
    }
};
```
#### 解法2
- 此处dp代表的是先发的人在[i,j]上能得到的有效分数（自己收益-对方收益）
- 根据dp状态转移方程，决定循环是正向还是逆向

>We can observe that the effective score for the current player for any given subarray nums$[x:y]$ only depends on the elements within the range $[x,y]$ in the array numsnums. It mainly depends on whether the element $nums[x]$ or $nums[y]$ is chosen in the current turn and also on the maximum score possible for the other player from the remaining subarray left after choosing the current element. Thus, it is certain that the current effective score isn't dependent on the elements outside the range $[x,y]$.
>
>Based on the above observation, we can say that if know the maximum effective score possible for the subarray $nums[x+1,y]$ and $nums[x,y-1]$, we can easily determine the maximum effective score possible for the subarray $nums[x,y]$ as $\text{max}(nums[x]-score_{[x+1,y]}, nums[y]-score_{[x,y-1]})$. These equations are deduced based on the last approach.
>
>From this, we conclude that we can make use of Dynamic Programming to determine the required maximum effective score for the array $nums$. We can make use of a 2-D dp array, such that $dp[i][j]$ is used to store the maximum effective score possible for the subarray $nums[i,j]$. The dp updation equation becomes:
>
> $dp[i,j] = \text{max}(nums[i] - dp[i + 1][j], nums[j] - dp[i][j - 1]dp[i,j]=nums[i]−dp[i+1][j],nums[j]−dp[i][j−1])$.

```
public class Solution {
    public boolean PredictTheWinner(int[] nums) {
        int[][] dp = new int[nums.length + 1][nums.length];
        for (int s = nums.length; s >= 0; s--) {
            for (int e = s + 1; e < nums.length; e++) {
                int a = nums[s] - dp[s + 1][e];
                int b = nums[e] - dp[s][e - 1];
                dp[s][e] = Math.max(a, b);
            }
        }
        return dp[0][nums.length - 1] >= 0;
    }
}
```