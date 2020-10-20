# 单调栈

## 介绍
- 中心思想：$O(N)$时间复杂度，找到数组内某个元素左/右第一个比自己大/小的元素
- 操作方法
  -   新建一个栈
  - 遍历数组内元素，找比某位置小的，则栈底小栈顶大；反正同理
  - 遍历途中如果发现某个数字不符合要求，弹出栈顶元素，结算之
---
## 例题

### [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)
- 此题生成一个水槽就可以结算，因此可以使用while循环。coding细节
```
class Solution {
public:
    int trap(vector<int>& height) {
        // 单调栈解法
        stack<int> mono_stack;
        // 当前位置左/右第一个比自己大的位置，存的下标
        int res=0, i=0;
        while ( i < height.size()){
            if ( mono_stack.empty() || height[i] <= height[mono_stack.top()]){
                mono_stack.push(i);
                i++;
            }
            else{
                // 弹出栈顶元素，准备结算该元素上产生的存水
                int cur_pos = mono_stack.top();
                mono_stack.pop();
                
                if (mono_stack.empty()){
                    continue; // 栈空，左边没有比当前高的，不产生存水
                }
                /*
                  存水 = 水槽深度 * 水槽宽度
                  此处解法，处理如 [3，1，2，3] 这种水槽形状，其实是从下往上处理
                  先处理2位置（1，宽度1）产生的存水量，再处理3，1，2，3上产生的存水量（深度3-2，宽度2）
                */
                int h = min(height[i], height[mono_stack.top()]);
                res += (h - height[cur_pos]) * ( i - mono_stack.top() -1);
            }
        }
        
        return res;
    }
};
```
---
### [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

> Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.
> 
> Example:
> 
> Input: [2,1,5,6,2,3]
> 
> Output: 10
- 此题不同于上一题。直方图内矩形，较低的在左右都可能生成矩形，所以用for套while循环写法
- 注意分析栈递增还是递减
- trick：原始输入最后加一个0，使得最后一个元素也能正常弹出结算
```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int res=0;
        //int i=0;
        stack<int> mono_stack;
        heights.push_back(0); // trick: 最后加一个0，最后一个数字可以结算
        // 求最大面积，虽然是找比自己大的问题，但是要从高矩形到低矩形求，否则会落可能性
        // 所以此处单调栈，遇到比栈顶小的，结算栈顶；栈底小顶大
        for ( int i=0; i < heights.size(); i++){
            while ( !mono_stack.empty() && heights[i] < heights[mono_stack.top()]){
                // 栈非空，满足不单调条件，一直弹出结算
                int h = heights[mono_stack.top()];
                mono_stack.pop();
                int left = -1;
                if ( !mono_stack.empty()){
                    left = mono_stack.top();
                }
                res = max( res, h * (i - left -1));
            }
            mono_stack.push(i);
        }
        return res;     
    }
};
```
---
### [402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/)

> Given a non-negative integer num represented as a string, remove k digits from the number so that the new number is the smallest possible.
> 
> Note:
> 
> The length of num is less than 10002 and will be ≥ k.
> 
> The given num does not contain any leading zero.
> 
> Example 1:
> 
> Input: num = "1432219", k = 3
> 
> Output: "1219"
> 
> Explanation: Remove the three digits 4, 3, and 2 to form the new number 1219 which is the smallest.
- 生成最小数字，在高位尽可能删除较大的数字。greedy
- 单调栈思想（递增）。扫描字符串，遇到比栈顶（当前res末位）小的数字就删掉末尾，入新数字。
- 此题利用`std::string`内的一些api来模仿真正的栈（push_back, pop_back, erase）
- coding细节，while的运用。
```
class Solution {
public:
    string removeKdigits(string num, int k) {
        string res = "";
        int length=num.size(), keep = length-k;
        for ( char c : num){
            while ( k != 0 && res.size() != 0 && res.back() > c){
                res.pop_back();
                k--;
            }
            res.push_back(c);
        }
        res.resize(keep);
        
        while ( res.size() != 0 && res[0] == '0'){
            res.erase(res.begin());
        }
        if ( res.empty()){
            return "0";
        }
        else{
            return res;
        }
    }
};
```
---
### [768. Max Chunks To Make Sorted II](https://leetcode.com/problems/max-chunks-to-make-sorted-ii/)

> Given an array arr of integers (not necessarily distinct), we split the array into some number of "chunks" (partitions), and individually sort each chunk.  After concatenating them, the result equals the sorted array.
> 
> What is the most number of chunks we could have made?
> 
> Example 2:
> 
> Input: arr = [2,1,3,4,4]
> 
> Output: 4
> 
> Explanation:
> 
> We can split into two chunks, such as [2, 1], [3, 4, 4].
> However, splitting into [2, 1], [3], [4], [4] is the highest number of chunks possible.
- 根据题意，最后目标数组为递增排序，考虑用单调栈
- 遍历`arr`，发现当前位置比栈顶大，当前位置可能是新块的起点，当前位置数字可能是新块内的最大值，入栈（可能的块数+1）。
- 发现当前比栈顶小，则当前数字不是新块起点。取cur_max出来。实际上是给小数字找个块塞进去。继续看栈内栈顶。如果比某个栈顶大，说明栈顶块不会包含此位置数字，则把cur_max压栈，说明较小数字已经被括在了一个块内。
```
class Solution {
public:
    int maxChunksToSorted(vector<int>& arr) {
        stack<int> mono_stack;
        int res = 0;
        for ( int i=0; i < arr.size(); i++){
            if ( mono_stack.empty() || arr[i] >= mono_stack.top()){
                mono_stack.push(arr[i]);
            }
            else{
                int cur_max = mono_stack.top();
                mono_stack.pop(); // 当前最大值
                while ( !mono_stack.empty() && arr[i] < mono_stack.top()){
                    mono_stack.pop();
                }
                mono_stack.push(cur_max);
            }
        }
        return mono_stack.size();
        
    }
};
```