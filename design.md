# 设计相关题目

### [146. LRU Cache](https://leetcode-cn.com/problems/lru-cache/)
> Design a data structure that follows the constraints of a Least Recently Used (LRU) cache.
> 
> Implement the LRUCache class:
> 
> - `LRUCache(int capacity)` Initialize the LRU cache with positive size capacity.
> - `int get(int key)` Return the value of the key if the key exists, otherwise return -1.
> - `void put(int key, int value)` Update the value of the key if the key exists. Otherwise, add the key-value pair to the cache. If the number of keys exceeds the capacity from this operation, evict the least recently used key.
- 解法：双向链表 + 哈希表（key -> 链表内节点）
- 此题`list`对象实现了两个功能：维护优先级队列和存储值。哈希表为了在$O(1)$实现更新频繁度功能
```c++
class LRUCache {
private:
    int cap;
    list<pair<int, int>> l; // l内每个pair是key-value
    unordered_map<int, list<pair<int, int>>::iterator> m; // m存储迭代器，key-链表内节点
public:
    LRUCache(int capacity) {
        cap = capacity;
    }
    
    int get(int key) {
        auto it = m.find(key); // auto指针应用
        if (it == m.end()){
            return -1; // c++ find api
        }
        // 把key对应节点移到链表开头
        // it->second是一个pair对象，api细节
        l.splice(l.begin(), l, it->second);
        return it->second->second; // pair对象的second指针
    }
    
    void put(int key, int value) {
        auto it = m.find(key);
        if (it != m.end()){
            l.erase(it->second); // erase掉原指针，准备覆盖
        }
        l.push_front(make_pair(key, value)); // 从左边（链表头）插入
        m[key] = l.begin(); // 按LRU要求，刷新该key的使用频繁程度
        if (m.size() > cap){
            int need_delete_key = l.rbegin()->first; // 此处用back不行，api问题
            m.erase(need_delete_key);
            l.pop_back();
        }
    }
};
```
---
### [384. Shuffle an Array 洗牌算法](https://leetcode-cn.com/problems/shuffle-an-array/)
Given an integer array nums, design an algorithm to randomly shuffle the array.

Implement the Solution class:

- `Solution(int[] nums)` Initializes the object with the integer array nums.
- `int[] reset()` Resets the array to its original configuration and returns it.
- `int[] shuffle()` Returns a random shuffling of the array.
  
蓄水池抽样思想
```c++
class Solution {
private:
    vector<int> arr;
public:
    Solution(vector<int>& nums) {
        arr.assign(nums.begin(), nums.end());
        
    }
    
    /** Resets the array to its original configuration and return it. */
    vector<int> reset() {
        return arr;
    }
    
    /** Returns a random shuffling of the array. */
    vector<int> shuffle() {
        vector<int> res = arr;
        for (int i=0; i < res.size(); i++){
            // 对遍历到的每个位置，随机选择其和后面某位置数字互换
            int t = i + rand() % (res.size()-i);
            swap(res[i], res[t]);
        }
        return res;
    }
};

/**
 * Your Solution object will be instantiated and called as such:
 * Solution* obj = new Solution(nums);
 * vector<int> param_1 = obj->reset();
 * vector<int> param_2 = obj->shuffle();
 */
```
---
### [382. Linked List Random Node](https://leetcode-cn.com/problems/linked-list-random-node/)
> Given a singly linked list, return a random node's value from the linked list. Each node must have the **same probability** of being chosen.
- 蓄水池抽样，同上
```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
private:
    ListNode* H;
public:
    /** @param head The linked list's head.
        Note that the head is guaranteed to be not null, so it contains at least one node. */
    Solution(ListNode* head) {
        H = head;
    }
    
    /** Returns a random node's value. */
    int getRandom() {
        int res = H->val, i=2;
        ListNode* cur = H->next;
        while(cur){
            int j = rand() % i;
            if (j == 0){
                res = cur->val;
            }
            i += 1;
            cur = cur->next;
        }
        return res;
    }
};

/**
 * Your Solution object will be instantiated and called as such:
 * Solution* obj = new Solution(head);
 * int param_1 = obj->getRandom();
 */
```
---
### [208. Implement Trie (Prefix Tree) 构建前缀树/Trie树](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)
- c++类的操作，见代码
```c++
class TrieNode {
public:
    TrieNode* child[26];
    bool isWord;
    TrieNode() :isWord(false){
        for (auto &a : child){
            a = NULL;
        }
    }
};

class Trie {
private:
    TrieNode* root;
public:
    /** Initialize your data structure here. */
    Trie() {
        root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    void insert(string word) {
        TrieNode* p = root;
        for (char c : word){
            int i = c - 'a';
            if (!p->child[i]){
                p->child[i] = new TrieNode();
            }
            p = p->child[i];
        }
        p->isWord = true;
    }
    
    /** Returns if the word is in the trie. */
    bool search(string word) {
        TrieNode* p = root;
        for (char c : word){
            int i = c - 'a';
            if (!p->child[i]) return false;
            p = p->child[i];
        }
        return p->isWord;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) {
        TrieNode* p = root;
        for (char c : prefix){
            int i = c - 'a';
            if (!p->child[i]) return false;
            p = p->child[i];
        }
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```