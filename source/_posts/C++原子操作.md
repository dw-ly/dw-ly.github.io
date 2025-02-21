---
title: C++原子操作
date: 2024-02-21
categories:
  - 编程
  - C++
tags:
  - C++
  - 并发编程
  - 原子操作
  - 内存序
---

# C++原子操作

## 基本概念

### 什么是原子操作
原子操作是指不可被中断的一个或一系列操作。在多线程环境中，原子操作可以保证数据的一致性，避免竞态条件。

普通操作 vs 原子操作：
```
普通操作（可能被中断）：
Thread1  Memory   Thread2
   |       |        |
   |--读值->|        |
   |       |<-读值--|
   |--改值->|        |
   |       |<-改值--|
   |       |        |
结果不确定！

原子操作（不可中断）：
Thread1  Memory   Thread2
   |       |        |
   |=原子操作=>|     |
   |       |        |
   |       |<=原子操作=|
   |       |        |
结果可预测！
```

### 原子操作的实现原理

CPU指令级别实现：
```
+---------------+
|  CPU指令级别  |
+-------+-------+
        |
+-------+-------+
|  CAS操作      |     内存屏障
|  Compare      |     +--------+
|  And Swap     |     |  Load  |
+---------------+     |  Store |
                     |  Full  |
                     +--------+
```

1. CAS (Compare-And-Swap) 操作
```cpp
bool CompareAndSwap(int* ptr, int oldValue, int newValue) {
    if (*ptr == oldValue) {
        *ptr = newValue;
        return true;
    }
    return false;
}
```

2. 内存屏障详解

#### 什么是内存屏障
内存屏障(Memory Barrier)是CPU或编译器在对内存随机访问的操作中的一个同步点，使得此点之前的所有读写操作都执行后才可以开始执行此点之后的操作。

没有内存屏障：
```
CPU1    Memory    CPU2
Store A   |        |
Store B   |        |
  |       |    Load B
  |       |    Load A
(可能乱序执行)
```

使用内存屏障：
```
CPU1    Memory    CPU2
Store A   |        |
===屏障===
Store B   |        |
  |       |    Load B
  |       |    ===屏障===
  |       |    Load A
(保证执行顺序)
```

内存序类型层次：
```
+------------------+
|   memory_order   |
+------------------+
         |
         +----------------> memory_order_relaxed (最宽松)
         |
         +----------------> memory_order_consume (数据依赖)
         |
         +----------------> memory_order_acquire (获取)
         |
         +----------------> memory_order_release (释放)
         |
         +----------------> memory_order_acq_rel (获取释放)
         |
         +----------------> memory_order_seq_cst (最严格)
```

内存序选择流程：
```
                        +----------------+
                        | 选择内存序     |
                        +----------------+
                               |
                        +----------------+
                        | 是否需要同步？  |
                        +----------------+
                          |          |
                     不需要         需要
                          |          |
                    relaxed     +----------------+
                               | 读还是写？      |
                               +----------------+
                                |    |    |
                               读    写   读写
                                |    |    |
                     +----------+    |    +----------+
                     |              |               |
              +----------------+  release         acq_rel
              | 是否有数据依赖？|
              +----------------+
                   |        |
                  是        否
                   |        |
                consume   acquire

完全有序的情况统一使用 seq_cst
```

1. memory_order_relaxed
- 最宽松的内存序
- 只保证当前操作的原子性
- 不提供同步或顺序保证
- 适用于计数器等简单场景

```cpp
std::atomic<int> counter(0);
counter.store(1, std::memory_order_relaxed);
int value = counter.load(std::memory_order_relaxed);
```

2. memory_order_consume
- 数据依赖序
- 保证当前读取操作与后续依赖此操作的读取操作的顺序
- 比acquire更轻量，但支持不够完善
- C++17后不推荐使用

```cpp
std::atomic<int*> ptr;
int data = 42;

// 线程1
ptr.store(&data, std::memory_order_release);

// 线程2
int* p = ptr.load(std::memory_order_consume);
if (p) {
    // 这里的*p读取操作会在ptr.load之后执行
    // 因为存在数据依赖关系
    assert(*p == 42);
}
```

3. memory_order_acquire
- 获取操作
- 当前读取操作必须在后续所有读写操作之前完成
- 可以和release配对使用
- 常用于读取共享数据

```cpp
std::atomic<bool> ready(false);
int data = 0;

// 线程1 - 写入线程
void producer() {
    data = 42;                                    // 写入共享数据
    ready.store(true, std::memory_order_release); // 发布
}

// 线程2 - 读取线程
void consumer() {
    while (!ready.load(std::memory_order_acquire)) {} // 获取
    assert(data == 42);                              // 安全读取
}
```

4. memory_order_release
- 释放操作
- 当前写入操作必须在之前的所有读写操作完成后进行
- 可以和acquire配对使用
- 常用于发布共享数据

```cpp
std::atomic<int*> ptr;
int data;

void producer() {
    data = 42;                                     // 准备数据
    ptr.store(&data, std::memory_order_release);   // 发布指针
}

void consumer() {
    int* p;
    while (!(p = ptr.load(std::memory_order_acquire))) {} // 等待指针
    assert(*p == 42);                              // 安全访问数据
}
```

5. memory_order_acq_rel
- 获取释放操作
- 结合了acquire和release的特性
- 适用于读修改写操作
- 确保操作前后的内存访问顺序

```cpp
std::atomic<int> shared(0);

void thread_func() {
    // 原子的读取当前值，加1，然后写回
    // 确保在此操作前的所有访问都完成，且此操作完成前不会开始后续访问
    int expected = shared.load(std::memory_order_relaxed);
    while (!shared.compare_exchange_strong(expected, 
                                         expected + 1,
                                         std::memory_order_acq_rel,
                                         std::memory_order_relaxed)) {
        // 重试直到成功
    }
}
```

6. memory_order_seq_cst
- 最严格的内存序
- 所有线程看到的操作顺序相同
- 是原子操作的默认内存序
- 性能开销最大但最安全

```cpp
std::atomic<bool> x(false), y(false);
std::atomic<int> z(0);

// 线程1
void thread1() {
    x.store(true, std::memory_order_seq_cst);
    if (y.load(std::memory_order_seq_cst)) {
        z.fetch_add(1, std::memory_order_seq_cst);
    }
}

// 线程2
void thread2() {
    y.store(true, std::memory_order_seq_cst);
    if (x.load(std::memory_order_seq_cst)) {
        z.fetch_add(1, std::memory_order_seq_cst);
    }
}
```

## std::atomic使用

### 基本用法
```cpp
#include <atomic>

std::atomic<int> counter(0);  // 原子整型
counter++;                    // 原子自增
counter.store(10);           // 原子存储
int value = counter.load();   // 原子读取
```

### 常用原子操作
```
原子操作类型：
+-------------+     +--------------+     +------------------+
| load/store  | --> |  exchange   | --> | compare_exchange |
+-------------+     +--------------+     +------------------+
                                              |
                                              v
                                    +------------------+     +------------------+
                                    |  fetch_add/sub  | --> | fetch_and/or/xor |
                                    +------------------+     +------------------+
```

```cpp
std::atomic<int> atom(0);

// 原子交换
int old = atom.exchange(5);

// 比较并交换
int expected = 5;
bool success = atom.compare_exchange_strong(expected, 10);

// 原子算术操作
int prev = atom.fetch_add(1);  // 自增并返回原值
prev = atom.fetch_sub(1);      // 自减并返回原值

// 原子位操作
prev = atom.fetch_and(0xFF);   // 按位与
prev = atom.fetch_or(0xFF);    // 按位或
prev = atom.fetch_xor(0xFF);   // 按位异或
```

## 内存序(Memory Order)

### 内存序类型
```
内存序类型层次：
+------------------+
|   memory_order   |
+------------------+
         |
         +----------------> memory_order_relaxed (最宽松)
         |
         +----------------> memory_order_consume (数据依赖)
         |
         +----------------> memory_order_acquire (获取)
         |
         +----------------> memory_order_release (释放)
         |
         +----------------> memory_order_acq_rel (获取释放)
         |
         +----------------> memory_order_seq_cst (最严格)
```

1. memory_order_relaxed
- 最宽松的内存序
- 只保证当前操作的原子性
- 不提供同步或顺序保证
- 适用于计数器等简单场景

```cpp
std::atomic<int> counter(0);
counter.store(1, std::memory_order_relaxed);
int value = counter.load(std::memory_order_relaxed);
```

2. memory_order_consume
- 数据依赖序
- 保证当前读取操作与后续依赖此操作的读取操作的顺序
- 比acquire更轻量，但支持不够完善
- C++17后不推荐使用

```cpp
std::atomic<int*> ptr;
int data = 42;

// 线程1
ptr.store(&data, std::memory_order_release);

// 线程2
int* p = ptr.load(std::memory_order_consume);
if (p) {
    // 这里的*p读取操作会在ptr.load之后执行
    // 因为存在数据依赖关系
    assert(*p == 42);
}
```

3. memory_order_acquire
- 获取操作
- 当前读取操作必须在后续所有读写操作之前完成
- 可以和release配对使用
- 常用于读取共享数据

```cpp
std::atomic<bool> ready(false);
int data = 0;

// 线程1 - 写入线程
void producer() {
    data = 42;                                    // 写入共享数据
    ready.store(true, std::memory_order_release); // 发布
}

// 线程2 - 读取线程
void consumer() {
    while (!ready.load(std::memory_order_acquire)) {} // 获取
    assert(data == 42);                              // 安全读取
}
```

4. memory_order_release
- 释放操作
- 当前写入操作必须在之前的所有读写操作完成后进行
- 可以和acquire配对使用
- 常用于发布共享数据

```cpp
std::atomic<int*> ptr;
int data;

void producer() {
    data = 42;                                     // 准备数据
    ptr.store(&data, std::memory_order_release);   // 发布指针
}

void consumer() {
    int* p;
    while (!(p = ptr.load(std::memory_order_acquire))) {} // 等待指针
    assert(*p == 42);                              // 安全访问数据
}
```

5. memory_order_acq_rel
- 获取释放操作
- 结合了acquire和release的特性
- 适用于读修改写操作
- 确保操作前后的内存访问顺序

```cpp
std::atomic<int> shared(0);

void thread_func() {
    // 原子的读取当前值，加1，然后写回
    // 确保在此操作前的所有访问都完成，且此操作完成前不会开始后续访问
    int expected = shared.load(std::memory_order_relaxed);
    while (!shared.compare_exchange_strong(expected, 
                                         expected + 1,
                                         std::memory_order_acq_rel,
                                         std::memory_order_relaxed)) {
        // 重试直到成功
    }
}
```

6. memory_order_seq_cst
- 最严格的内存序
- 所有线程看到的操作顺序相同
- 是原子操作的默认内存序
- 性能开销最大但最安全

```cpp
std::atomic<bool> x(false), y(false);
std::atomic<int> z(0);

// 线程1
void thread1() {
    x.store(true, std::memory_order_seq_cst);
    if (y.load(std::memory_order_seq_cst)) {
        z.fetch_add(1, std::memory_order_seq_cst);
    }
}

// 线程2
void thread2() {
    y.store(true, std::memory_order_seq_cst);
    if (x.load(std::memory_order_seq_cst)) {
        z.fetch_add(1, std::memory_order_seq_cst);
    }
}
```

## 实践示例

### 原子计数器
```cpp
class AtomicCounter {
    std::atomic<int> count{0};
public:
    void increment() {
        count.fetch_add(1, std::memory_order_relaxed);
    }
    
    int get() const {
        return count.load(std::memory_order_acquire);
    }
};
```

### 双重检查锁定模式
```cpp
class Singleton {
    static std::atomic<Singleton*> instance;
    static std::mutex mutex;
    
public:
    static Singleton* getInstance() {
        Singleton* tmp = instance.load(std::memory_order_acquire);
        if (tmp == nullptr) {
            std::lock_guard<std::mutex> lock(mutex);
            tmp = instance.load(std::memory_order_relaxed);
            if (tmp == nullptr) {
                tmp = new Singleton;
                instance.store(tmp, std::memory_order_release);
            }
        }
        return tmp;
    }
};
```

## 注意事项

1. 原子操作不等于线程安全
- 原子操作只保证单个操作的原子性
- 复合操作可能需要额外的同步机制

2. 性能考虑
- 原子操作比普通操作慢
- 不同的内存序对性能影响不同
- 应该在必要时才使用原子操作

3. 调试建议
- 使用内存检测工具
- 仔细测试不同的线程交错情况
- 考虑使用更高级的同步机制 