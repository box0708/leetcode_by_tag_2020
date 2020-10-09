## [524. Longest Word in Dictionary through Deleting](https://leetcode.com/problems/longest-word-in-dictionary-through-deleting/)

> Given a string and a string dictionary, find the longest string in the dictionary that can be formed by deleting some characters of the given string. If there are more than one possible results, return the longest word with the smallest lexicographical order. If there is no possible result, return the empty string.
> 
> Example 1:
> Input:
> s = "abpcplea", d = ["ale","apple","monkey","plea"]
> 
> Output: 
> "apple"

```
class Solution {
public:
    string findLongestWord(string s, vector<string>& d) {
        string res = "";
        for ( string str : d){
            // 这算是第一个指针
            int index = 0;
            for ( char c : s){
                if ( index < str.size() && c==str[index]){
                    // 配合题意，用一个指针扫描候选串，模拟候选操作
                    index ++;
                }
            }
            // index指针走到字符串结尾，证明经过删除操作，能得出候选串
            if ( index == str.size() && str.size() >= res.size()){
                if ((str.size() > res.size()) || (str < res)){
                    res = str;
                }
            }
        }
        return res;
    }
};
```