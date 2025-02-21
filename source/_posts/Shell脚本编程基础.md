---
title: Shell脚本编程基础
date: 2024-02-21
categories:
  - 语言
  - 脚本语言
  - Shell
tags:
  - Shell
  - 脚本语言
  - Linux
  - 入门
  - 教程
  - 运维
  - 自动化
---

## Shell 脚本编程基础

本文介绍 Shell 脚本的基础知识和常用命令。

### 1. 基础语法

#### 1.1 变量定义和使用
```bash
# 变量定义
name="Shell"
number=42

# 变量使用
echo "Hello, $name!"
echo "Number is ${number}"
```

#### 1.2 条件判断
```bash
# if 语句
if [ $number -gt 0 ]; then
    echo "正数"
elif [ $number -eq 0 ]; then
    echo "零"
else
    echo "负数"
fi

# case 语句
case $1 in
    "start")
        echo "启动服务"
        ;;
    "stop")
        echo "停止服务"
        ;;
    *)
        echo "未知命令"
        ;;
esac
```

### 2. 循环结构
```bash
# for 循环
for i in {1..5}; do
    echo $i
done

# while 循环
count=1
while [ $count -le 5 ]; do
    echo $count
    let count++
done
```

### 3. 函数
```bash
# 函数定义
function greet() {
    echo "Hello, $1!"
}

# 函数调用
greet "World"
```

### 4. 文件操作
```bash
# 读取文件
while read line; do
    echo "$line"
done < input.txt

# 写入文件
echo "新内容" > output.txt
echo "追加内容" >> output.txt
```

更多 Shell 脚本编程内容将在后续文章中详细介绍。
