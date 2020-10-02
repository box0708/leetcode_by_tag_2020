# 滑动窗口类题目

## [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
> You are given an array of integers nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position.
>
> Return the max sliding window.
> Example 1:
> 
> Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
> Output: [3,3,5,5,6,7]

返回滑动窗口最大值数组
- 注意res数组大小
- 中心思想：双端队列头始终是当前窗口最大值
- 遍历过程中，当前数比队尾大就一直弹（保存小的没意义）
- 边界
- trick：双端队列存数组下标

```
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        if (nums.size() == 0 || k==0 || k > nums.size()){
            vector<int> res; // 异常保护
            return res;
        }
        vector<int> res(nums.size()-k+1, 0); // res数组，自定义好size
        deque<int> helper; // 辅助：双端队列
        
        int cur_index=0;
        for ( int i=0; i < nums.size(); i++){
            while(!helper.empty() && nums[helper.back()] <= nums[i]){
            /*
              双端队列不为空，且当前遍历到的数>=队尾，弹出队尾
            */
                helper.pop_back();
            }
            helper.push_back(i); // 进入双端队列
            if (helper.front() == i-k){
                helper.pop_front(); // 队列头过期，弹出队列头
            }
            if (i >= k-1){
            // 已经形成窗口，队列首的值进入res
                res[cur_index] = nums[helper.front()];
                cur_index++;
            }
        }
        return res;
    }
};
```

---

# [最大值减去最小值小于或等于num的子数组数量](https://www.nowcoder.com/questionTerminal/5fe02eb175974e18b9a546812a17428e)

> 给定数组 arr 和整数 num，共返回有多少个子数组满足如下情况：
> max(arr[i...j]) - min(arr[i...j]) <= num
> max(arr[i...j])表示子数组arr[i...j]中的最大值，min[arr[i...j])表示子数组arr[i...j]中的最小值。
>
> 输入
> 5 2 
> 1 2 3 4 5
> 输出
> 12

```
#include <bits/stdc++.h>
using namespace std;
 
int main(){
    int n, m;
    cin>>n>>m;
    int a[n], cnt=n;
    for(int i=0;i<n;i++){
        cin>>a[i];
    }
    // 双端队列，存下标
    deque<int> minq;
    deque<int> maxq;
    
    int left=0, right=0, res=0;
    while(left < n){
       while (right < n){
       // 维护两个滑动窗口结构，此处注意用right下标
            while (!maxq.empty() && a[right] >= a[maxq.back()]){
               maxq.pop_back();
           }
           maxq.push_back(right);
           while (!minq.empty() && a[right] <= a[minq.back()]){
               minq.pop_back();
           }
           minq.push_back(right);
           
           if (a[maxq.front()] - a[minq.front()] > m){
               break;
           }
           right++;
       }
        
        if (minq.front() == left){
            minq.pop_front();
        }
        if (maxq.front() == left){
            maxq.pop_front();
        }
        // 最右边结束，计算当前res加多少
        res += (right-left);
        left++;
    }
    
    
    cout<<res<<endl;
    return 0;
    
}
```
---
## [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)

> Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).
> 
> Example:
> 
> Input: S = "ADOBECODEBANC", T = "ABC"
> Output: "BANC"
```
class Solution {
public:
    string minWindow(string s, string t) {
        int left=0; // 当前满足包含t所有字母的起点
        int cnt=0; // cnt==t.length 表示已经包含了t内所有字母
        int minLeft = -1;
        int minLen = INT_MAX;
        vector<int> letterCnt(128,0); // trick:char类型的处理
        for (char c:t){
            letterCnt[c] += 1;
        }
        
        for ( int i=0; i < s.size(); i++){
            if (--letterCnt[s[i]] >= 0){
                cnt += 1; // 如果当前右边界遍历到的字母，
                            //map内减一>=0, 说明命中了t内字母，cnt++
            }
            while ( cnt == t.size()){
                // 当前窗口达标
                int cur_len = i - left + 1;
                if (minLen > cur_len){
                    minLen = cur_len;
                    minLeft = left;
                }
                // 左边界往右移动，是否达标。
                // 不达标了，cnt--，退出循环
                if ( ++letterCnt[s[left]] > 0){
                    cnt -=1;
                }
                left++;
            }
        }
        if (minLeft == -1){
            return "";
        }
        else{
            return s.substr(minLeft, minLen);
        }
    }
};
```
