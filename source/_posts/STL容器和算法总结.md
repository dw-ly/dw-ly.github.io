---
title: STL容器和算法总结
date: 2024-02-21
categories:
  - 语言
  - 编程语言
  - C++
  - STL
tags:
  - C++
  - STL
  - 容器
  - 算法
  - 数据结构
  - 进阶
---

# STL容器和算法总结

## 顺序容器

### vector
```cpp
// 动态数组
vector<int> vec;
vec.push_back(1);     // 尾部添加元素
vec.pop_back();       // 删除尾部元素
vec.size();           // 获取大小
vec[0];              // 随机访问

// 初始化方式
vector<int> vec1 = {1, 2, 3};
vector<int> vec2(5, 0);  // 5个0
```

### list
```cpp
// 双向链表
list<int> lst;
lst.push_back(1);     // 尾部添加
lst.push_front(0);    // 头部添加
lst.pop_back();       // 尾部删除
lst.pop_front();      // 头部删除
```

#### 1.3 deque
```cpp
// 双端队列
deque<int> dq;
dq.push_back(1);      // 尾部添加
dq.push_front(0);     // 头部添加
dq[0];               // 随机访问
```

### 2. 关联容器

#### 2.1 set/multiset
```cpp
// 有序集合
set<int> s;
s.insert(1);          // 插入元素
s.erase(1);           // 删除元素
s.find(1);            // 查找元素

// 允许重复元素
multiset<int> ms;
```

#### 2.2 map/multimap
```cpp
// 键值对映射
map<string, int> m;
m["key"] = 1;         // 插入/修改
m.erase("key");       // 删除
m.find("key");        // 查找

// 允许重复键
multimap<string, int> mm;
```

### 3. 无序容器

#### 3.1 unordered_set/unordered_multiset
```cpp
// 哈希集合
unordered_set<int> us;
us.insert(1);         // O(1)插入
us.find(1);           // O(1)查找
```

#### 3.2 unordered_map/unordered_multimap
```cpp
// 哈希映射
unordered_map<string, int> um;
um["key"] = 1;        // O(1)插入/修改
um.find("key");       // O(1)查找
```

### 4. 容器适配器

```cpp
// 栈
stack<int> st;
st.push(1);           // 压栈
st.pop();             // 出栈
st.top();             // 查看栈顶

// 队列
queue<int> q;
q.push(1);            // 入队
q.pop();              // 出队
q.front();            // 队首
q.back();             // 队尾

// 优先队列
priority_queue<int> pq;
pq.push(1);           // 入队
pq.top();             // 查看最大值
```

### 5. 常用算法

#### 5.1 排序算法
```cpp
// 排序
sort(vec.begin(), vec.end());                    // 升序
sort(vec.begin(), vec.end(), greater<int>());    // 降序

// 部分排序
partial_sort(vec.begin(), vec.begin()+3, vec.end()); // 部分排序

// 堆操作
make_heap(vec.begin(), vec.end());               // 建堆
push_heap(vec.begin(), vec.end());               // 添加元素
pop_heap(vec.begin(), vec.end());                // 删除最大元素
```

#### 5.2 查找算法
```cpp
// 二分查找
binary_search(vec.begin(), vec.end(), value);    // 是否存在
lower_bound(vec.begin(), vec.end(), value);      // 第一个不小于
upper_bound(vec.begin(), vec.end(), value);      // 第一个大于

// 查找元素
find(vec.begin(), vec.end(), value);             // 线性查找
find_if(vec.begin(), vec.end(), pred);           // 条件查找
```

#### 5.3 修改算法
```cpp
// 复制
copy(src.begin(), src.end(), dest.begin());      // 复制
copy_if(src.begin(), src.end(), dest.begin(), pred); // 条件复制

// 填充
fill(vec.begin(), vec.end(), value);             // 填充值
generate(vec.begin(), vec.end(), gen);           // 生成值

// 删除
remove(vec.begin(), vec.end(), value);           // 移除值
remove_if(vec.begin(), vec.end(), pred);         // 条件移除
```

### 6. 使用技巧

1. 选择合适的容器
   - 频繁随机访问：vector
   - 频繁插入删除：list
   - 两端操作：deque
   - 关联查找：map/set
   - 哈希查找：unordered_map/set

2. 注意性能影响
   - vector 扩容成本
   - list 的内存开销
   - map/set 的平衡树调整
   - 哈希表的冲突处理

3. 迭代器失效
   - 插入/删除后注意迭代器有效性
   - 容器扩容后迭代器可能失效
   - 使用 erase 的返回值更新迭代器

更多 STL 使用技巧和进阶特性将在后续文章中详细介绍。
