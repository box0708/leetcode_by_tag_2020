# 回溯
[参考资料](https://labuladong.gitbook.io/algo/di-ling-zhang-bi-du-xi-lie/hui-su-suan-fa-xiang-jie-xiu-ding-ban)
回溯类模板如下
> ```python
> result = []
>def backtrack(路径, 选择列表):
>    if 满足结束条件:
>        result.add(路径)
>        return
>
>    for 选择 in 选择列表:
>        做选择
>        backtrack(路径, 选择列表)
>        撤销选择
> ```
---
### [17. Letter Combinations of a Phone Number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

> Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent. Return the answer **in any order**.
> 
> A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.
> ```
> Example 1:
> Input: digits = "23"
> Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
> ```

```c++
class Solution {
public:
    vector<string> dict = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    vector<string> letterCombinations(string digits) {
        vector<string> res;
        string tmp;
        if (digits.empty()){
            return res;
        }
        dfs(res, tmp, digits, 0);
        return res;
    }
    void dfs(vector<string>& res, string& tmp, string& digits, int start){
        if ( start == digits.size() && tmp.size() == digits.size()){
            res.push_back(tmp);
            return;
        }
        int index = digits[start] - '2';
        for ( char c : dict[index]){
            tmp.push_back(c);
            dfs(res, tmp, digits, start+1);
            tmp.pop_back();
        }
        return;
    }
};
```
---
### [22. Generate Parentheses](https://leetcode-cn.com/problems/generate-parentheses/)
> Given $n$ pairs of parentheses, write a function to generate all combinations of well-formed parentheses.
> ```
> Example 1:
>Input: n = 3
>Output: ["((()))","(()())","(())()","()(())","()()()"]
> ```

```c++
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> res;
        string tmp;
        if (n == 0){
            return res;
        }
        // n pairs of parentheses; n*'(' and n*')'
        helper(res, tmp, 0, 0, n);
        return res;
    }
    void helper(vector<string>& res, string& tmp, int leftNum, int rightNum, int n){
        if (tmp.size() == 2*n){
            res.push_back(tmp);
            return;
        }
        if ( leftNum < n){
            tmp.push_back('(');
            helper(res, tmp, leftNum+1, rightNum, n);
            tmp.pop_back();
        }
        if ( rightNum < leftNum){
            // 注意此处特殊，先有左括号，才能进右括号
            tmp.push_back(')');
            helper(res, tmp, leftNum, rightNum+1, n);
            tmp.pop_back();
        }
        return;
    }
};
```
---
### [39. Combination Sum](https://leetcode-cn.com/problems/combination-sum/)
> Given an array of distinct integers `candidates` and a target integer `target`, return a list of all unique combinations of candidates where the chosen numbers sum to `target`. You may return the combinations in any order.
> 
> The same number may be chosen from candidates an unlimited number of times. Two combinations are unique if the frequency of at least one of the chosen numbers is different.
> 
> It is guaranteed that the number of unique combinations that sum up to target is less than 150 combinations for the given input.
> ```
> Example 1:
> Input: candidates = [2,3,6,7], target = 7
> Output: [[2,2,3],[7]]
> Explanation:
> 2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
> 7 is a candidate, and 7 = 7.
> These are the only two combinations.
> ```
- 排序，剪枝处理
- 可使用多次的处理方法
```c++
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end()); // 预先排序，剪枝
        vector<vector<int>> res;
        vector<int> tmp;
        if (candidates.empty()){
            return res;
        }
        helper(candidates, res, tmp, target, 0);
        return res;
    }
    void helper(vector<int>& candidates, vector<vector<int>>& res, vector<int>& tmp, int target, int index){
        if ( target < 0){
            return;
        }
        if ( target == 0){
            res.push_back(tmp);
            return;
        }
        for ( int i=index; i < candidates.size(); i++){
            if ( candidates[i] > target){
                break;
            }
            tmp.push_back(candidates[i]);
            helper(candidates, res, tmp, target-candidates[i], i); // 从i开始，表示同一个数字可以用多次
            tmp.pop_back();
        }
        return;
    }
};
```
---
### [40. Combination Sum II](https://leetcode-cn.com/problems/combination-sum-ii/)
> Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in candidates where the candidate numbers sum to target.
> 
> Each number in candidates may only be used once in the combination.
> 
> ***Note: The solution set must not contain duplicate combinations.***
- 注意此题去重（剪枝）细节。对输入排序是剪枝的前提。
```c++
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        vector<int> tmp;
        if (candidates.empty()){
            return res;
        }
        sort(candidates.begin(), candidates.end());
        helper(candidates, res, tmp, target, 0);
        return res;
    }
    void helper(vector<int>& candidates, vector<vector<int>>& res, vector<int>& tmp, int target, int index){
        if ( target < 0){
            return;
        }
        if ( target == 0){
            res.push_back(tmp);
            return;
        }
        for ( int i=index; i < candidates.size(); i++){
            if ( candidates[i] > target){
                break;
            }
            if ( i && candidates[i] == candidates[i-1] && i > index){
                continue;
            }
            tmp.push_back(candidates[i]);
            helper(candidates, res, tmp, target - candidates[i], i+1);
            tmp.pop_back();
        }
    }
    
};
```
---
### [46. Permutations(全排列1)](https://leetcode-cn.com/problems/permutations/)
> Given an array `nums` of distinct integers, return all the possible permutations. You can return the answer in any order.

![Alt text](https://camo.githubusercontent.com/61a3a48be6ec6c616ff14160a28e17dcd62f48cb341539d2307b58fadda8efe9/68747470733a2f2f7069632e6c656574636f64652d636e2e636f6d2f306266313866396238366132353432643166366161386462366363343534373566636535616133323961303763613032613933353763326561643831656563312d696d6167652e706e67 "optional title")
```c++
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        vector<bool> used(nums.size());
        if ( nums.empty()){
            return res;
        }
        helper(res, nums, temp, used);
        return res;
    }
    void helper(vector<vector<int>>& res, vector<int>& nums, vector<int>& temp, vector<bool>& used){
        if ( temp.size() == nums.size()){
            res.push_back(temp);
            return;
        }
        for ( int i=0; i < nums.size(); i++){
            if (!used[i]){
                temp.push_back(nums[i]);
                used[i] = true;
                helper(res, nums, temp, used);
                used[i] = false;
                temp.pop_back();
            }
        }
    }
};
```
---
### [47. Permutations II(全排列带重复)](https://leetcode-cn.com/problems/permutations-ii/)
> Given a collection of numbers, `nums`, that *might contain duplicates*, return all possible unique permutations in any order.

```c++
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        vector<bool> used(nums.size());
        if ( nums.size() == 0){
            return res;
        }
        sort(nums.begin(), nums.end()); // 先排序，然后可以剪枝
        helper(res, nums, temp, used);
        return res;
    }
    
    void helper(vector<vector<int>>& res, vector<int>& nums, vector<int>& temp, vector<bool>& used){
        if ( temp.size() == nums.size()){
            res.push_back(temp);
            return;
        }
        for ( int i=0; i < nums.size(); i++){
            if(!used[i]){
                if ( i && nums[i] == nums[i-1] && !used[i-1]){
                    continue; // 解决重复
                }
                used[i] = true;
                temp.push_back(nums[i]);
                helper(res, nums, temp, used);
                temp.pop_back();
                used[i] = false;
            }
        }
    }
};
```
### [77. Combinations(组合问题)](https://leetcode-cn.com/problems/combinations/)
> Given two integers `n` and `k`, return all possible combinations of `k` numbers out of $1 ... n$.
> 
> You may return the answer in any order.
- 组合问题不需要`used`数组表示是否选中，要增加`start`指针。
```c++
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> res;
        vector<int> temp;
        // vector<bool> used(n);
        helper(n, k, res, temp, 1);
        return res;
    }
    void helper(int n, int k, vector<vector<int>>& res, vector<int>& temp, int start){
        if (temp.size() == k){
            res.push_back(temp);
            return;
        }
        for ( int i=start; i <= n; i++ ){
            temp.push_back(i);
            helper(n, k, res, temp, i+1);
            temp.pop_back();
        }
    }
};
```
---
### [78. Subsets(子集问题)](https://leetcode-cn.com/problems/subsets/)
> Given an integer array `nums`, return all possible subsets (the power set).
> 
> The solution set must not contain duplicate subsets.

```c++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> temp;
        helper(res, nums, temp, 0);
        return res;
    }
    void helper(vector<vector<int>>& res, vector<int>& nums, vector<int>& temp, int index){
        if ( index == nums.size()){
            res.push_back(temp);
            return;
        }
        // 要当前数字
        temp.push_back(nums[index]);
        helper(res, nums, temp, index+1);
        // 不要当前数字
        temp.pop_back();
        helper(res, nums, temp, index+1);
    }
};
```
---
### [90. Subsets II](https://leetcode-cn.com/problems/subsets-ii/)
> Given a collection of integers that might contain duplicates, nums, return all possible subsets (the power set).
> 
> Note: The solution set must not contain duplicate subsets.
- 注意coding细节
```c++
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end()); // 输入有重复，先排序才能剪枝
        vector<vector<int>> res;
        vector<int> temp;
        helper(res, nums, temp, 0);
        return res;
    }
    void helper(vector<vector<int>>& res, vector<int>& nums, vector<int>& temp, int start){
         if ( start > nums.size()) {
             return;
         }
        res.push_back(temp);
        for ( int i=start; i < nums.size(); i++){
            if ( i != start && nums[i] == nums[i-1]){
                continue; // 输入有重复，常规操作
            }
           
           temp.push_back(nums[i]);
           helper(res, nums, temp, i+1); // 这里非常容易错
           temp.pop_back();
        }
    }
};
```
---
### [131. Palindrome Partitioning](https://leetcode-cn.com/problems/palindrome-partitioning/)
> Given a string `s`, partition s such that every substring of the partition is a palindrome. Return all possible palindrome partitioning of s.
> 
> A palindrome string is a string that reads the same backward as forward.

```c++
class Solution {
public:
    vector<vector<string>> partition(string s) {
        vector<vector<string>> res;
        vector<string> temp;
        helper(res, temp, s, 0);
        return res;
    }
    void helper(vector<vector<string>>& res, vector<string>& temp, string& s, int start){
        if ( start == s.size()){
            res.push_back(temp);
            return;
        }
        for( int i=start; i < s.size(); i++){
            if (!check_pali(s, start, i)){
                continue;
            }
            temp.push_back(s.substr(start, i-start+1));
            helper(res, temp, s, i+1); // 递归条件注意
            temp.pop_back();
        }
    }
    
    
    bool check_pali(string s, int start, int end){
        while ( start < end){
            if (s[start] != s[end]){
                return false;
            }
            start += 1;
            end -= 1;
        }
        return true;
    }
};
```