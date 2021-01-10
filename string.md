# 字符串相关题目

### [43. Multiply Strings 大数乘法](https://leetcode-cn.com/problems/multiply-strings/)
> Given two non-negative integers num1 and num2 represented as strings, return the product of num1 and num2, also represented as a string.
- 按位乘，注意进位技巧
- 注意高位0处理技巧
```c++
class Solution {
public:
    string multiply(string nums1, string nums2) {
        vector<int> res(nums1.size()+nums2.size(), 0);
        string ans;
        for (int i=nums1.size()-1; i >=0; i--){
            for (int j=nums2.size()-1; j >=0; j--){
                // string末尾（数字低位）开始计算
                res[i+j+1] += (nums1[i]-'0') * (nums2[j]-'0');
                res[i+j] += res[i+j+1]/10;
                res[i+j+1] = res[i+j+1]%10;
            }
        }
        bool zero = false;
        for (int c : res){
            if (c || zero){
                zero = true;
                ans += to_string(c);
            }
        }
        if (ans == ""){
            return "0";
        }
        return ans;
    }
};
```
---

### [49. Group Anagrams](https://leetcode-cn.com/problems/group-anagrams/)
> Given an array of strings strs, group the anagrams together. You can return the answer in any order.
> 
> An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> res;
        unordered_map<string, int> m;
        for (string s : strs){
            string tmp = s;
            sort(tmp.begin(), tmp.end()); // anagram排序后是唯一的字符串
            if (!m.count(tmp)){
                m[tmp] = res.size(); // map不含当前‘唯一字符串’，就把在res中的下标存进去    
                res.push_back({});
            }
            res[m[tmp]].push_back(s);
        }
        return res;
    }
};
```