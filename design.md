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