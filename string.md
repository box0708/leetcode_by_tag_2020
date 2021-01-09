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