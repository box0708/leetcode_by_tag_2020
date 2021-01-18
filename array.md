# 数组相关题目

## N Sum

### [18. 4Sum 四数之和==target](https://leetcode-cn.com/problems/4sum/)

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> res;
        if (nums.empty()){
            return res;
        }
        int n = nums.size();
        sort(nums.begin(), nums.end());
        for (int i=0; i < n-3; i++){
            if (i && nums[i] == nums[i-1]){continue;}
            for (int j = i+1; j < n-2; j++){
                if (j != i+1 && nums[j]==nums[j-1]){continue;}
                int left=j+1, right=n-1;
                while (left < right){
                    if (nums[i]+nums[j]+nums[left]+nums[right] == target){
                        res.push_back(vector<int>({nums[i],nums[j],nums[left],nums[right]}));
                        while(left < right && nums[left] == nums[left+1]){left++;}
                        while(left < right && nums[right] == nums[right-1]){right--;}
                        left++;
                        right--;
                    }
                    else if (nums[i]+nums[j]+nums[left]+nums[right] > target){right--;}
                    else{left++;}
                }
            }
        }
        return res;
    }
};
```
---
### [15. 3Sum](https://leetcode-cn.com/problems/3sum/)
> Given an array `nums` of n integers, are there elements $a, b, c$ in nums such that $a + b + c = 0$? Find all unique triplets in the array which gives the sum of zero. Notice that the solution set must not contain duplicate triplets.
- 双指针解法
```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        vector<int> tmp;
        int len = nums.size();
        if (len == 0){
            return res;
        }
        sort(nums.begin(), nums.end()); // 去重的前提
        for (int i=0; i < len; i++){
            if (i && nums[i] == nums[i-1]){
                continue; // 去重
            }
            int left = i+1, right = len-1;
            while(left < right){
                // 类似二分查找的思想
                if (nums[i] + nums[left] + nums[right] == 0){
                    res.push_back({nums[i], nums[left], nums[right]});
                    left++;
                    right--;
                    while (left < right && nums[left] == nums[left-1]){
                        left++;
                    }
                    while (left < right && nums[right] == nums[right+1]){
                        right--;
                    }
                }
                else if (nums[i] + nums[left] + nums[right] > 0){
                    right--;
                }
                else{
                    left++;
                }
            }
        }
        return res;
    }
};
```
---
### [454. 4Sum II](https://leetcode-cn.com/problems/4sum-ii/)
> Given four lists A, B, C, D of integer values, compute how many tuples (i, j, k, l) there are such that A[i] + B[j] + C[k] + D[l] is zero.
```
Example:
Input:
A = [ 1, 2]
B = [-2,-1]
C = [-1, 2]
D = [ 0, 2]

Output:
2
Explanation:
The two tuples are:
1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
```
- 哈希表省时间 + 取反
```c++
class Solution {
public:
    int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
        int res = 0;
        unordered_map<int, int> abs;
        for (int a : A){
            for (int b : B){
                ++abs[a+b];
            }
        }
        for (int c : C){
            for (int d : D){
                int tmp = -c-d;
                if (abs.count(tmp)){
                    res += abs[tmp];
                }
            }
        }
        return res;
    }
};
```
---
### [41. First Missing Positive](https://leetcode-cn.com/problems/first-missing-positive/)
> Given an unsorted integer array nums, find the smallest missing positive integer.
> ```
> Example:
> Input: nums = [1,2,0]
> Output: 3
> ```
>

- 把1放在`nums[0]`，2放在`nums[1]`，...（即如果`nums[i] > 0`, `nums[i]` 在`nums[nums[i]-1]`）
```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        for (int i=0; i < n; i++){
            while (nums[i] > 0 && nums[i] <= n && nums[i] != nums[nums[i]-1]){
            //while (nums[i] > 0 && nums[i] <= n && i != nums[i]-1){
            // 上面写法会死循环
                swap(nums[i], nums[nums[i]-1]);
            }
        }
        for (int i=0; i < n; i++){
            if (nums[i] != i+1){
                return i+1;
            }
        }
        return n+1;
    }
};
```
---
### [287. Find the Duplicate Number](https://leetcode-cn.com/problems/find-the-duplicate-number/)
> Given an array of integers nums containing `n + 1` integers where each integer is in the range `[1, n]` inclusive.
> 
> There is **only one repeated number** in nums, return this repeated number.
> ```
> Example 1
> Input: nums = [1,3,4,2,2]
> Output: 2
> ```
- 第一次尝试上一题的思路，不ac
- 二分查找思想
```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int left=1, right=nums.size()-1;
        while(left <= right){
            // 限定数字[1,n]，先找中点，再遍历nums，看<=mid的个数
            int cnt=0, mid = left + (right-left)/2;
            for (int num : nums){
                if (num <= mid){
                    cnt += 1;
                }
            }
            if (cnt <= mid){
                left = mid+1;
            }
            else{
                right = mid-1;
            }
        }
        return left;
    }
};
```
---
### [48. Rotate Image](https://leetcode-cn.com/problems/rotate-image/)
> You are given an *n x n* 2D `matrix` representing an image, rotate the image by 90 degrees (clockwise).
- 注意下标
```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        if (matrix.size() <= 1){
            return;
        }
        int n = matrix.size();
        for (int i=0; i < n/2; i++){
            for (int j=i; j < n-1-i; j++){
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[n-1-j][i];
                matrix[n-1-j][i] = matrix[n-1-i][n-1-j];
                matrix[n-1-i][n-1-j] = matrix[j][n-1-i];
                matrix[j][n-1-i] = tmp;
            }
        }
    }
};
```
---
### [54. Spiral Matrix 转圈打印矩阵](https://leetcode-cn.com/problems/spiral-matrix/)
> Given an m x n matrix, return all elements of the matrix in spiral order.
- 同上一题，边界控制比较巧妙
```c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> res;
        int m=matrix.size(), n=matrix[0].size();
        int up=0, down=m-1, left=0, right=n-1;
        while (1){
            for (int j=left; j <= right; j++){
                res.push_back(matrix[up][j]);
            }
            up++;
            if (up > down) break;
            for (int i=up; i <= down; i++){
                res.push_back(matrix[i][right]);
            }
            right--;
            if (right < left) break;
            for (int j=right; j >= left; j--){
                res.push_back(matrix[down][j]);
            }
            down--;
            if (down < up) break;
            for (int i=down; i >= up; i--){
                res.push_back(matrix[i][left]);
            }
            left++;
            if (left > right) break;
        }
        return res;
    }
};
```
---
### [66. Plus One 模拟加法加一](https://leetcode-cn.com/problems/plus-one/)
```
Example 1:

Input: digits = [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.
```

```c++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int n = digits.size();
        for (int i=n-1; i >= 0; i--){
            if (digits[i] == 9){
                digits[i] = 0; // 解决9的问题，不需要设置进位变量一个一个遍历
            }
            else{
                digits[i] += 1;
                return digits;
            }
        }
        //digits.insert(digits.begin(), 1);
        digits[0] = 1;
        digits.push_back(0);
        return digits;
    }
};
```
---
### [128. Longest Consecutive Sequence 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)
> Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence.
```
Example 1:

Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
```
- 比较巧妙
  - 全部数字塞进HashSet，然后处理一个erase一个（剪枝）
  - `next`和`prev`是两个边界，往两边扩散
```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int res=0;
        unordered_set<int> s(nums.begin(), nums.end()); // api使用
        for (int num : nums){
            if (!s.count(num)){
                continue;
            }
            s.erase(num); // 剪枝
            int prev=num-1, next=num+1;
            while(s.count(prev)){
                s.erase(prev); // 剪枝
                prev--;
            }
            while(s.count(next)){
                s.erase(next); //剪枝
                next++;
            }
            res = max(res, next-prev-1);
        }
        return res;
    }
};
```
---
### [136. Single Number](https://leetcode-cn.com/problems/single-number/)
> 只出现一次的数字
- 异或的应用。
```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        if (nums.empty()){
            return 0;
        }
        int num = nums[0];
        for (int i=1; i < nums.size(); i++){
            num = num ^ nums[i];
        }
        return num;
    }
};
```
---
### [150. Evaluate Reverse Polish Notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
> 逆波兰表达式求值
```
Example 1:
Input: ["2", "1", "+", "3", "*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```
```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> nums;
        for (string s : tokens){
            if (s != "+" && s != "-" && s != "*" && s != "/"){
                nums.push(stoi(s)); // string转int
            }
            else{
                int op2 = nums.top();
                nums.pop();
                int op1 = nums.top();
                nums.pop();
                if ( s == "+"){
                    nums.push(op1 + op2);
                    continue;
                }
                if ( s == "-"){
                    nums.push(op1 - op2);
                    continue;
                }
                if ( s == "*"){
                    nums.push(op1 * op2);
                    continue;
                }
                if ( s == "/"){
                    nums.push(op1 / op2);
                    continue;
                }
            }
        }
        return nums.top();
    }
};
```
---
### [152. Maximum Product Subarray](https://leetcode-cn.com/problems/maximum-product-subarray/)
> Given an integer array nums, find the contiguous subarray within an array (containing at least one number) which has the largest product.
```
Example 1:
Input: [2,3,-2,4]
Output: 6
Explanation: [2,3] has the largest product 6.
```
```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if (nums.empty()){
            return 0;
        }
        // 最大乘积三种可能：正*cur正，负*cur负，cur
        int cur_min=INT_MAX, cur_max=INT_MIN;
        int pre_min=nums[0], pre_max=nums[0], res=nums[0];
        for (int i=1; i < nums.size(); i++){
            cur_min = min(min(pre_min*nums[i], pre_max*nums[i]), nums[i]);
            cur_max = max(max(pre_min*nums[i], pre_max*nums[i]), nums[i]);
            pre_min = cur_min;
            pre_max = cur_max;
            res = max(res, cur_max);
        }
        return res;
    }
};
```
---
### [179. Largest Number](https://leetcode-cn.com/problems/largest-number/)
> Given a list of non-negative integers nums, arrange them such that they form the largest number.
```
Example 2:
Input: nums = [3,30,34,5,9]
Output: "9534330"
```
- `sort`函数自定义比较器的写法
  - >总结起来就是：
     sort函数根据comp函数的返回值，对comp函数的两个参数排序
     >
     > 如果comp返回true，排序为“参数1”“参数2”，否则排序为“参数2”“参数1”。
     >
     > 想要升序排列，则`return parameter1<parameter2`
     >
     > 想要降序排列，则`return parameter1>parameter2`
```c++
class Solution {
public:
    string largestNumber(vector<int>& nums) {
        if (nums.empty()) return "0";
        if (nums.size() ==1){
            return to_string(nums[0]);
        }
        /*
            sort比较逻辑说明：
            例如3，30。string类型的比较中“330” > “303”
        */
        sort(nums.begin(), nums.end(), [](int a, int b){
            return to_string(a) + to_string(b) > to_string(b) + to_string(a);
        });
        string res = "";
        for (int i=0; i < nums.size(); i++){
            res += to_string(nums[i]);
        }
        if (res[0] == '0'){
            return "0"; // 避免输入是[0，0，0，0，0]
        }
        return res;
    }
};
```
---
### [189. Rotate Array](https://leetcode-cn.com/problems/rotate-array/)
> Given an array, rotate the array to the right by k steps, where k is non-negative.
```
Example 1:
Input: nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]
```
```c++
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        int len = nums.size();
        k = k % len;
        if (len <= 1 || k==0){
            return;
        }
        reverse(nums, 0, len-1);
        reverse(nums, 0, k-1);
        reverse(nums, k, len-1);
        
    }
    void reverse(vector<int>& nums, int L, int R){
        while (L <= R){
            swap(nums[L], nums[R]);
            L++;
            R--;
        }
    }
};
```
---
### [238. Product of Array Except Self](https://leetcode-cn.com/problems/product-of-array-except-self/)
> Given an array `nums` of `n` integers where n > 1,  return an array output such that `output[i]` is equal to the product of all the elements of nums except `nums[i]`.
```
Example:
Input:  [1,2,3,4]
Output: [24,12,8,6]
```
- 除自己的乘积 = 左半边乘积*右半边乘积，注意两头边界值处理
```c++
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        // 乘积 = 一个数字左半边乘积 * 右半边乘积
        int n = nums.size();
        vector<int> res(n,1); // 含义：除自己外，左半边累乘结果
        for (int i=0; i < n-1; i++){
            res[i+1] = res[i] * nums[i]; // 涉及i+1，注意边界
        }
        int right_p = 1;
        for (int i=n-1; i >= 0; i--){
            res[i] = res[i] * right_p;
            right_p = right_p * nums[i]; 
        }
        return res;
    }
};
```
---
### [334. Increasing Triplet Subsequence](https://leetcode-cn.com/problems/increasing-triplet-subsequence/)
> Given an integer array `nums`, return `true` if there exists a triple of indices `(i, j, k)` such that `i < j < k` and `nums[i] < nums[j] < nums[k]`. If no such indices exists, return `false`.
- 巧妙。随着数组访问模拟了`f1 < f2 < num`这个过程
```c++
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        if (nums.size() <= 2){
            return false;
        }
        int f1=INT_MAX, f2=INT_MAX;
        for (int num : nums){
            if (f1 >= num){
                f1 = num;
            }
            else if ( f2 >= num){
                f2 = num;
            }
            else{
                return true;
            }
        }
        return false;
    }
};
```
---
## prefix-sum array技巧
### [560. Subarray Sum Equals K](https://leetcode-cn.com/problems/subarray-sum-equals-k/)
>Given an array of integers nums and an integer k, return the total number of continuous subarrays whose sum equals to k.
```
Example 1:
Input: nums = [1,1,1], k = 2
Output: 2
```
- 遍历过程计算当前sum，并建立sum到路径树的哈希表
- $[0,i]$范围和为sum，之前某一`j`下标出现过`sum-k`，则可求和为`k`的数量
```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> hasSum;
        hasSum[0] = 1;
        int sum = 0, res=0;
        for (int num : nums){
            sum += num;
            if (hasSum.count(sum-k)){
                res += hasSum[sum-k];
            }
            ++hasSum[sum];
        }
        return res;
    }
};
```
---
### [4. Median of Two Sorted Arrays](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)
> Given two sorted arrays `nums1` and `nums2` of size m and n respectively, return the *median* of the two sorted arrays.
- 找两个排序数组的中位数，要求时间复杂度 $O(log(m+n))$，考虑二分查找
- 考虑两个数组总长度m+n的奇偶，中位数计算分2种情况。`left`和`right`是一个技巧，无论奇偶都能cover
- 中心思想：不merge，在两个数组中找第k小的数字 -> 分别在两个数组找第k/2小的数字，比较大小 -> 一次删掉k/2个不可能的数字，继续递归。[参考资料](https://www.cnblogs.com/grandyang/p/4465932.html)
```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m=nums1.size(), n=nums2.size(), left=(m+n+1)/2, right=(m+n+2)/2; // trick here
        // find kth
        return (find_Kth(nums1, 0, nums2, 0, left) + find_Kth(nums1, 0, nums2, 0, right)) / 2.0;
    }
    int find_Kth(vector<int>& nums1, int i, vector<int>& nums2, int j, int k){
        // 在nums1和nums2混合的数组中求从小到大第k个数字
        if (i >= nums1.size()){
            return nums2[j + k -1];
        }
        if (j >= nums2.size()){
            return nums1[i + k -1];
        }
        if (k == 1){
            return min(nums1[i], nums2[j]);
        }
        // 开始正戏
        int mid1 = INT_MAX, mid2 = INT_MAX;
        if ((i + k/2 -1) < nums1.size()){
            mid1 = nums1[i + k/2 -1];
        }
        if ((j + k/2 -1) < nums2.size()){
            mid2 = nums2[j + k/2 -1];
        }
        if (mid1 > mid2){
            return find_Kth(nums1, i, nums2, j+k/2, k-k/2);
        }
        else{
            return find_Kth(nums1, i+k/2, nums2, j, k-k/2);
        }
    }
};
```
