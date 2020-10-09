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
- 此题生成一个池子就可以结算，因此可以使用while循环
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
                  此处解法，处理如 [0，3，1，2，3] 这种水槽形状，其实是从上往下处理
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