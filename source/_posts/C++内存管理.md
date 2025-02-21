---
title: C++内存管理
date: 2025-02-21
categories:
  - 编程
  - C++
tags:
  - C++
  - 内存管理
  - 内存池
  - 性能优化
---

# C++内存管理

## 内存分区

### 内存布局概览
```
高地址    +------------------+
         |    内核空间      |
         +------------------+
         | 命令行参数和环境变量|
         +------------------+
         |    栈区(Stack)   |
         |        ↓        |
         |                 |
         |        ↑        |
         |     堆区(Heap)   |
         +------------------+
         |  未初始化数据(.bss)|
         +------------------+
         |  已初始化数据(.data)|
         +------------------+
         |   代码段(.text)   |
低地址    +------------------+
```

### 内存区域划分
```
+------------------+
| 命令行参数和环境变量|
+------------------+
|    栈区(Stack)   |
+------------------+
|    堆区(Heap)    |
+------------------+
| 未初始化数据(BSS) |
+------------------+
| 已初始化数据(Data)|
+------------------+
|   代码区(Text)   |
+------------------+
```

### 栈区(Stack)
```
高地址    +------------------+
         |     参数 n       |
         +------------------+
         |     参数 2       |
         +------------------+
         |     参数 1       |
         +------------------+
         |    返回地址      |
         +------------------+
         |   上一帧指针     |
         +------------------+
         |   局部变量 1     |
         +------------------+
         |   局部变量 2     |
low地址    +------------------+
```

- 由编译器自动分配和释放
- 存放函数参数、局部变量等
- 在编译时确定大小
- 函数执行完毕后自动释放
- 空间较小但分配效率高

```cpp
void func() {
    int a;          // 栈上的局部变量
    int arr[10];    // 栈上的数组
}  // 函数结束时自动释放
```

### 堆区(Heap)
```
+------------------+
|   已分配块 1     |
+------------------+
|    空闲块 1      |
+------------------+
|   已分配块 2     |
+------------------+
|    空闲块 2      |
+------------------+

内存块结构：
+------------------+
|  块大小/标志位    |
+------------------+
|    实际数据      |
+------------------+
|    填充字节      |
+------------------+
```

- 由程序员手动分配和释放
- 使用 malloc/free 或 new/delete
- 运行时动态分配
- 需要手动管理内存
- 空间较大但分配效率较低

```cpp
int* p = new int;      // 堆上分配内存
delete p;              // 手动释放内存

int* arr = new int[10];  // 堆上分配数组
delete[] arr;            // 释放数组
```

### 自由存储区(Free Store)
- C++中通过 new/delete 操作的内存
- 是一个抽象概念
- 默认由堆实现
- 可通过重载 new/delete 自定义实现

```cpp
class MyClass {
public:
    void* operator new(size_t size) {
        return malloc(size);  // 自定义分配
    }
    void operator delete(void* ptr) {
        free(ptr);           // 自定义释放
    }
};
```

### 全局/静态存储区
- 存放全局变量和静态变量
- 程序运行期间一直存在
- 程序结束时由系统释放
- 分为初始化和未初始化区

```cpp
int global = 1;         // 全局变量
static int s_value = 2; // 静态变量

class Test {
    static int count;   // 静态成员变量
};
```

### 常量存储区
- 存放字符串常量和const常量
- 只读不可修改
- 程序结束时由系统释放

```cpp
const int MAX = 100;           // const常量
char* str = "Hello World";     // 字符串常量
```

## 内存分配方式

### 静态分配
- 编译时确定大小
- 存储于静态存储区
- 生命周期贯穿程序运行
```cpp
static int value = 100;
const int MAX_SIZE = 1000;
```

### 栈分配
- 编译时确定大小
- 自动分配和释放
- 效率高但空间有限
```cpp
void func() {
    int array[1000];    // 栈上分配
    struct Point p;     // 栈上的结构体
}
```

### 堆分配
- 运行时动态分配
- 手动管理内存
- 灵活但需要注意内存泄漏
```cpp
// C风格
int* p1 = (int*)malloc(sizeof(int));
free(p1);

// C++风格
int* p2 = new int(42);
delete p2;
```

## 内存池技术

### 基本概念
```
内存池工作流程：
+---------------+     +--------------+     +------------+
| 大块内存申请  | --> | 分割成小块   | --> | 空闲链表  |
+---------------+     +--------------+     +------------+
                                               |
                            +------------------+
                            |
                     +------------+     +------------+
                     | 内存分配  | <-- | 内存释放   |
                     +------------+     +------------+
```

内存池是一种内存分配策略,预先分配一大块内存,然后自己管理内存的分配和释放。

### 实现原理
```cpp
class MemoryPool {
private:
    struct Block {
        Block* next;
    };
    
    Block* freeList;    // 空闲块链表
    void* memory;       // 内存池起始地址
    size_t blockSize;   // 块大小
    size_t poolSize;    // 池大小

public:
    MemoryPool(size_t blockSize, size_t blockCount) {
        this->blockSize = blockSize;
        this->poolSize = blockSize * blockCount;
        
        // 分配内存池
        memory = malloc(poolSize);
        freeList = (Block*)memory;
        
        // 初始化空闲链表
        char* block = (char*)memory;
        for(size_t i = 0; i < blockCount - 1; ++i) {
            Block* current = (Block*)block;
            current->next = (Block*)(block + blockSize);
            block += blockSize;
        }
        ((Block*)(block))->next = nullptr;
    }
    
    void* allocate() {
        if(freeList == nullptr) return nullptr;
        
        void* result = freeList;
        freeList = freeList->next;
        return result;
    }
    
    void deallocate(void* ptr) {
        if(ptr == nullptr) return;
        
        Block* block = (Block*)ptr;
        block->next = freeList;
        freeList = block;
    }
};
```

### 使用场景
1. 频繁分配释放固定大小的内存
2. 对内存分配性能要求高的场景
3. 需要减少内存碎片的场景

## 内存问题及调试

### 内存问题概览
```
+------------------+
| 内存问题        |
+------------------+
| 内存泄漏        |
+------------------+
| 野指针          |
+------------------+
| 重复释放        |
+------------------+
| 内存越界        |
+------------------+
| 踩内存          |
+------------------+

+------------------+
| 内存泄漏        |
+------------------+
| 资源未释放      |
+------------------+
+------------------+
| 野指针          |
+------------------+
| 指向无效地址    |
+------------------+
+------------------+
| 重复释放        |
+------------------+
| 多次释放同一地址|
+------------------+
+------------------+
| 内存越界        |
+------------------+
| 访问超出范围    |
+------------------+
+------------------+
| 踩内存          |
+------------------+
| 访问非法地址    |
+------------------+
```

#### 内存泄漏
- 分配的内存未释放
- 指针丢失导致无法释放
```cpp
void memoryLeak() {
    int* p = new int(42);
    // 忘记 delete p
}  // p离开作用域,内存泄漏
```

#### 野指针
- 指向已释放的内存
- 使用未初始化的指针
```cpp
int* p;           // 未初始化
*p = 42;          // 危险!

int* q = new int;
delete q;
*q = 42;          // 危险!已释放的内存
```

#### 重复释放
```cpp
int* p = new int;
delete p;
delete p;    // 错误:重复释放
```

#### 内存越界
```cpp
int arr[5];
arr[5] = 42;  // 越界访问
```

#### 踩内存
- 访问了不应该访问的内存地址
- 常见原因:
  - 数组越界
  - 指针未初始化
  - 使用已释放的内存
  - 栈溢出

```cpp
// 数组越界
int arr[5];
arr[5] = 1;    // 越界访问

// 使用已释放的内存
int* p = new int;
delete p;
*p = 42;       // 使用已释放的内存

// 栈溢出
void recursion() {
    int arr[1000000];  // 大数组可能导致栈溢出
    recursion();       // 无限递归导致栈溢出
}
```

### 调试技巧

#### 使用调试工具
1. Valgrind
```bash
valgrind --leak-check=full ./program
```

2. AddressSanitizer
```cpp
// 编译时启用
g++ -fsanitize=address program.cpp
```

#### 代码检查
1. 智能指针
```cpp
std::unique_ptr<int> p(new int(42));  // 自动管理内存
std::shared_ptr<int> sp = std::make_shared<int>(42);
```

2. RAII技术
```cpp
class ResourceGuard {
    Resource* res;
public:
    ResourceGuard() : res(new Resource()) {}
    ~ResourceGuard() { delete res; }  // 自动释放
};
```

#### 踩内存排查方法
1. 使用调试工具
```bash
# 使用 Valgrind 检测
valgrind --tool=memcheck ./program

# 使用 AddressSanitizer
g++ -fsanitize=address -g program.cpp
```

2. 代码审查
- 检查数组边界
- 确保指针初始化
- 避免使用已释放的内存
- 注意递归深度

3. 防范措施
```cpp
// 使用 vector 代替原始数组
std::vector<int> vec(5);

// 使用智能指针避免内存泄漏
std::shared_ptr<int> p = std::make_shared<int>(42);

// 使用 RAII 管理资源
class ArrayGuard {
    std::vector<int> arr;
public:
    ArrayGuard(size_t size) : arr(size) {}
    // 自动管理内存
};
```

#### 最佳实践
1. 入参进行边界检查
```cpp
void process(int* arr, size_t size, size_t index) {
    if (index >= size) {
        throw std::out_of_range("Index out of bounds");
    }
    arr[index] = 42;
}
```

2. 避免代码嵌套太深
```cpp
// 不好的实践
void deepNested(int* p) {
    if (p) {
        if (*p > 0) {
            if (*p < 100) {
                // 嵌套太深,容易出错
            }
        }
    }
}

// 好的实践
void flattenCode(int* p) {
    if (!p) return;
    if (*p <= 0 || *p >= 100) return;
    // 主要逻辑
}
```

3. 合理使用局部变量
```