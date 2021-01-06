# [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

>Given a collection of intervals, merge all overlapping intervals.
>
>Example 1:
>
>Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
>Output: [[1,6],[8,10],[15,18]]
>Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
>
>Example 2:
>
>Input: intervals = [[1,4],[4,5]]
>Output: [[1,5]]
>Explanation: Intervals [1,4] and [4,5] are considered overlapping.

- 先对输入排序
- vector最后一个元素携带信息

```
class Solution {
public:
    
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> result;
        if ( intervals.size() == 0){
            return result;
        }
        
        stable_sort(intervals.begin(), intervals.end());
        
        for ( int i=0; i<intervals.size(); i++){
            if (result.empty()){
                result.push_back(intervals[i]);
                continue;
            }
            
            int cur_left = intervals[i][0];
            int cur_right = intervals[i][1];
            int last_right = result.back()[1];
            
            if (cur_left > last_right){
                result.push_back(intervals[i]);
            }
            else{
                result.back()[1] = max(last_right, cur_right);
            }
            
        }
        return result;
        
    }
};
```
---
# [57. Insert Interval](https://leetcode.com/problems/insert-interval/)

> Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).
>
> You may assume that the intervals were initially sorted according to their start times.
>
> Example 1:
>
> Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
> Output: [[1,5],[6,9]]
> Example 2:
> 
> Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
> Output: [[1,2],[3,10],[12,16]]
> Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].

- coding细节，newInterval变量的处理
- 向两边扩范围

```
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        
        vector<vector<int>> result;
        if (intervals.size() == 0){
            result.push_back(newInterval);
            return result;
        }
        
        for ( int i=0; i<intervals.size(); i++){
            vector<int> cur = intervals[i];
            if ( cur[1] < newInterval[0]){
                result.push_back(cur); // cur在待插入区间前，cur插入
            }
            else if ( cur[0] > newInterval[1]){
                // cur待插入区间后，插入二者中靠前的那个（此时是cur）；转移待插入区间（newInterval变量的处理）
                result.push_back(newInterval);
                newInterval = cur;
            }
            else{
                // cur和待插入区间出现重叠。重叠未完全结束前，newInterval变量向两边扩。
                // 重叠完全处理完之后，插入操作在上两个if处理
                newInterval[0] = min(cur[0], newInterval[0]);
                newInterval[1] = max(cur[1], newInterval[1]);
            }
            
        }
        result.push_back(newInterval); // 因为有newInterval = cur，需要这个操作
        return result;
    }
};
```
---
# [986. Interval List Intersections](https://leetcode-cn.com/problems/interval-list-intersections/)
> Given two lists of closed intervals, each list of intervals is pairwise disjoint and in sorted order.
> 
> Return the intersection of these two interval lists.
> ```
> Input: A = [[0,2],[5,10],[13,23],[24,25]], B = [[1,5],[8,12],[15,24],[25,26]]
> Output: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
> ```
- 双指针解题法
```c++
class Solution {
public:
    vector<vector<int>> intervalIntersection(vector<vector<int>>& A, vector<vector<int>>& B) {
        vector<vector<int>> res;
        if (A.empty() || B.empty()){
            return res;
        }
        int lena = A.size(), lenb = B.size();
        int ia=0, ib=0;
        while (ia != lena && ib != lenb){
            vector<int> curA = A[ia];
            vector<int> curB = B[ib];
            if (curA[1] < curB[0]){
                ia++;
            }
            else if (curA[0] > curB[1]){
                ib++;
            }
            else{
                vector<int> tmp;
                tmp.push_back(max(curA[0], curB[0]));
                tmp.push_back(min(curA[1], curB[1]));
                res.push_back(tmp);
                if (curA[1] <= curB[1]){
                    ia++;
                }
                else{
                    ib++;
                }
            }
        }
        return res;
    }
};
```


