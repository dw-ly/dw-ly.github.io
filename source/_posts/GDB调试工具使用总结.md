---
title: GDB调试工具使用总结
date: 2024-02-21
categories:
  - 工具
  - 调试工具
tags:
  - GDB
  - 调试
  - C++
  - Linux
  - 开发工具
  - 实践
---

# GDB调试工具使用总结

## 断点操作

### 设置断点
```bash
# 普通断点
b file.cpp:21          # 在 file.cpp 第 21 行设置断点

# 条件断点
b debug.cpp:14 if c=4  # 当 c=4 时触发断点

# 数据断点
b *0x400522           # 根据地址设置断点
b &变量名             # 根据变量设置断点

# 函数断点
b funcName            # 在函数入口设置断点

# 偏移量断点
b +偏移量             # 根据当前位置偏移设置断点

# 监视点
watch *地址           # 监视内存地址变化
watch var            # 监视变量值变化
watch (condition)    # 监视条件变化
```

### 管理断点
```bash
# 查看断点
i b                   # 显示所有断点信息

# 删除断点
d 4                   # 删除断点号为 4 的断点
delete                # 删除所有断点
d 1 2                # 删除断点 1 和 2
d 5-10               # 删除断点 5 到 10

# 使用 clear 删除
clear test_fun        # 删除指定函数的所有断点
clear test.cpp:33     # 删除指定行的断点

# 启用/禁用断点
enable 断点编号        # 启用断点
disable 断点编号       # 禁用断点
enable once 断点编号   # 启用一次后自动禁用
enable count N 断点编号 # 启用 N 次后自动禁用
ignore 断点编号 次数    # 忽略前 N 次触发
```

## 程序运行控制

### 基本控制
```bash
# 启动程序
r/run                 # 运行程序
attach pid           # 附加到已运行的进程

# 继续运行
c/continue           # 继续运行到下一个断点
```

### 单步调试
```bash
n/next              # 单步执行（不进入函数）
s/step              # 单步执行（进入函数）
finish              # 运行至当前函数返回
```

### 多线程调试
```bash
# 线程操作
info threads        # 显示所有线程
thread 线程ID        # 切换到指定线程
thread apply all 命令 # 对所有线程执行命令
set scheduler-locking off/on/step  # 设置线程调度模式
```

## 查看和修改数据

### 变量操作
```bash
# 打印变量
p 变量名             # 打印变量值
p *(struct node*)地址 # 按特定类型打印指针内容
x/nfu 地址          # 查看内存内容

# 修改变量
set var 变量名=值    # 修改变量值
```

## GDB 配置

### Core 文件配置
```bash
# 开启 core 文件生成
ulimit -c unlimited

# 设置 core 文件路径
echo "/corefile/core-%e-%p-%t" > /proc/sys/kernel/core_pattern
```

### .gdbinit 配置文件
```bash
# 常用配置示例
set print pretty on          # 格式化打印结构体
set print object on         # 打印对象详细信息
set print static-members on # 打印静态成员
set print array on         # 打印数组元素
set print elements 0       # 打印字符串完整内容
```

## 调试技巧

### 基本技巧
1. 使用临时断点（`tb`）进行一次性调试
2. 使用条件断点定位特定场景
3. 使用监视点跟踪内存变化
4. 使用 `finish` 快速跳出当前函数
5. 合理使用断点启用/禁用管理调试流程

### 高级技巧
```cpp
// 添加调试信息
class Base {
public:
    virtual void func() {
        std::cout << "Base::func" << std::endl;
    }
};

// 使用 sizeof 和偏移量
std::cout << "Size of Base: " << sizeof(Base) << std::endl;
std::cout << "Offset of data: " << offsetof(Base, data) << std::endl;
```

## 常见问题处理

### 符号调试
- 确保编译时使用 `-g` 选项
- 检查符号表文件是否存在

### Core 文件问题
- 检查系统限制：`ulimit -c`
- 确认文件系统权限

### 多线程问题
- 使用 `set scheduler-locking on` 锁定线程
- 检查死锁情况

更多 GDB 调试技巧和进阶用法将在后续文章中详细介绍。
