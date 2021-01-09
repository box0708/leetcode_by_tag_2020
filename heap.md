# 应用堆模式题目
## 双堆模式
### [295. Find Median from Data Stream](https://leetcode-cn.com/problems/find-median-from-data-stream/)
> Design a data structure that supports the following two operations:
> 
> - `void addNum(int num)` - Add a integer number from the data stream to the data structure.
> 
> - `double findMedian()` - Return the median of all elements so far.
- 维护大根堆（数组比较小的半边）和小根堆（数组比较大的半边）
- 大根堆size == 小根堆size 或者大根堆比小根堆多一个
```c++
class MedianFinder {
public:
    /** initialize your data structure here. */
    MedianFinder() {

    }
    
    void addNum(int num) {
        left.push(num);
        right.push(left.top()); // 此时最大值已经处理完，不需要额外写逻辑
        left.pop();
        // 这块应该可以剪枝优化
        if ( right.size() > left.size()){
            left.push(right.top());
            right.pop();
        }
    }
    
    double findMedian() {
        if (left.size() == right.size()){
            return (left.top() + right.top())/2.0;
        }
        else{
            return left.top();
        }
    }
private:
    priority_queue<int> left; // 大根堆，左
    priority_queue<int, vector<int>, greater<int>> right; // 小根堆，右

};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder* obj = new MedianFinder();
 * obj->addNum(num);
 * double param_2 = obj->findMedian();
 */
```
---
### [480. Sliding Window Median 滑动窗口中位数](https://leetcode-cn.com/problems/sliding-window-median/)
> Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.
> 
> Given an array `nums`, there is a sliding window of size `k` which is moving from the very left of the array to the very right. You can only see the `k` numbers in the window. Each time the sliding window moves right by one position. Your job is to output the median array for each window in the original array.
- 一些api的细节
- 本题small和large并不是上一题最大、最小堆的意思，就是排序数组的较小一半和较大一半，保持较小一半最多多一个元素
```c++
class Solution {
public:
    vector<double> medianSlidingWindow(vector<int>& nums, int k) {
        multiset<int> large, small; // 此处small和large代表窗口排序后的前半部分和后半部分
        vector<double> res;
        for (int i=0; i < nums.size(); i++){
            // 窗口过期逻辑处理
            if (i >= k){
                if (small.count(nums[i-k])){
                    small.erase(small.find(nums[i-k])); // api细节：erase的输入是一个指针
                }
                else if (large.count(nums[i-k])){
                    large.erase(large.find(nums[i-k])); // api细节：erase的输入是一个指针
                }
            }
            // 求中位数，0 <= 前半段size - 后半段size <=1
            if (small.size() <= large.size()){
                if (large.empty() || nums[i] <= *large.begin()){
                    small.insert(nums[i]); // 当前值比较大部分最小值小
                }
                else{
                    // 较大部分最小值弹出，入左边
                    small.insert(*large.begin());
                    large.erase(*large.begin());
                    large.insert(nums[i]);
                }
            }
            else{
                if (nums[i] >= *small.rbegin()){
                    large.insert(nums[i]);
                }
                else{
                    large.insert(*small.rbegin());
                    small.erase(--small.end()); // api细节：small.end()是尾指针，要先--
                    small.insert(nums[i]);
                }
            }
            if (i >= k-1){
                if (k % 2){
                    res.push_back(*small.rbegin());
                }
                else {
                    res.push_back(((double)*small.rbegin() + *large.begin()) / 2);
                }
            }
        }
        return res;
    }
};
```
---
## 前`K`大的数

### [215. Kth Largest Element in an Array](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)
> Find the `k`th largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

```c++
class Solution {
public:
    // 解法1 堆排序
    // 解法2 快排
    int findKthLargest(vector<int>& nums, int k) {
        int left=0, right=nums.size()-1;
        while (1){
            int pos = partition(nums, left, right);
            if (pos == nums.size()-k){ // 下标变换，第k大，第k小
                return nums[pos];
            }
            else if (pos > nums.size()-k){
                right = pos-1;
            }
            else{
                left = pos+1;
            }
        }
    }
    int partition(vector<int>& nums, int L, int R){
        int flag = nums[R], less=L-1, more=R+1, cur = L;
        while (cur < more){
            if (nums[cur] == flag){
                cur++;
            }
            else if (nums[cur] < flag){
                swap(nums[cur++], nums[++less]);
            }
            else{
                swap(nums[cur], nums[--more]);
            }
        }
        return more-1; // 排好序的区间，当前左边界
    }
};
```
---
### [347. Top K Frequent Elements](https://leetcode-cn.com/problems/top-k-frequent-elements/)
> Given a non-empty array of integers, return the `k` most frequent elements.
- c++ api的使用
```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> m;
        priority_queue<pair<int, int>> q;
        vector<int> res;
        for (int i : nums){
            ++m[i];
        }
        for (auto it : m){
            q.push(make_pair(it.second, it.first));
        }
        for (int i=0; i < k; i++){
            res.push_back(q.top().second);
            q.pop();
        }
        return res;
    }
};
```
---
### [373. Find K Pairs with Smallest Sums](https://leetcode-cn.com/problems/find-k-pairs-with-smallest-sums/)

> You are given two integer arrays `nums1` and `nums2` sorted in ascending order and an integer `k`.
> 
> Define a pair $(u,v)$ which consists of one element from the first array and one element from the second array.
> 
> Find the k pairs $(u1,v1),(u2,v2) ...(uk,vk)$ with the smallest sums.
- 自定义比较器 + 优先级队列的使用
```c++
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        vector<vector<int>> res;
        priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> q;
        for (int i=0; i < min(k, (int)nums1.size()); i++){
            for (int j=0; j < min(k, (int)nums2.size()); j++){
                if (q.size() < k){
                    q.push(make_pair(nums1[i], nums2[j]));
                }
                else if (nums1[i] + nums2[j] < q.top().first + q.top().second){
                    q.push(make_pair(nums1[i], nums2[j]));
                    q.pop();
                }
            }
        }
        while (!q.empty()){
            vector<int> tmp = {q.top().first, q.top().second};
            res.push_back(tmp);
            q.pop();
        }
        return res;
    }
    struct cmp {
        bool operator() (pair<int, int> &a, pair<int, int> &b){
            // 按需要定义比较器
            return a.first+a.second < b.first+b.second;
        }
    };
};
```